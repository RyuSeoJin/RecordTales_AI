# Experience System — 경험치 시스템

> **문서 성격**: `experience` 시스템의 **경험치** 스펙.
> 길드 레벨 1~10 성장 시스템. 퀘스트 완료·집중 세션으로 XP를 획득하여 레벨업하고, 자동/구매 해금 요소를 활성화한다.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 📑 목차

1. [개요](#1-개요)
2. [UI 구조](#2-ui-구조)
3. [데이터 모델](#3-데이터-모델)
4. [동작 규칙](#4-동작-규칙)
5. [사용자 상호작용](#5-사용자-상호작용)
6. [관련 시스템](#6-관련-시스템)

---

## 1. 개요

- **한 줄 정의**: 길드 레벨 1~10 성장 시스템. 퀘스트 완료·집중 세션으로 XP를 획득하여 레벨업, 자동/구매 해금 요소 활성화.
- **위치**: guild-info 패널 (XP 바), 해금 모달
- **구현 상태**: ✅ 구현 완료

사용자가 퀘스트를 완료하거나 집중 세션(타이머/스톱워치)을 마치면 XP를 획득한다. 누적 XP가 레벨별 요구치를 충족하면 길드 레벨이 상승하며, 레벨에 따라 해금 요소가 자동 적용되거나 구매 가능해진다. 길드 레벨은 1에서 시작하여 최대 10까지 성장하고, 레벨마다 고유 칭호가 부여된다.

## 2. UI 구조

### 2.1. 와이어프레임

**guild-info 패널 내 XP 표시**

```
┌────────────────────────────────┐
│  [G]  길드명                    │
│       Lv.3  ████████░░░░  64%  │  ← XP bar (3px, gold gradient)
│       "정식 길드마스터"          │  ← 칭호
│       120 / 180 XP             │  ← XP 텍스트 (DM Mono 8px)
└────────────────────────────────┘
```

**레벨업 순간**

```
  중앙 sigil에 flash 애니메이션 (1.5초)
  상단 toast: "Lv.4 달성!"
```

### 2.2. CSS 클래스 구조

```
.guild-xp-wrap
  └ .guild-xp-bar          ← XP 진행 바 (3px, gold gradient)
.guild-xp-text              ← XP 수치 텍스트 (DM Mono 8px)
.sigil-letter.level-up-flash ← 레벨업 시 sigil flash 애니메이션
```

### 2.3. 시각 요소 상세

| 요소 | 속성 |
|------|------|
| XP 바 | 높이 3px, gold gradient 배경 |
| XP 텍스트 | DM Mono, 8px |
| 레벨업 애니메이션 | sigil에 `level-up-flash` 클래스 적용, 1.5초 후 제거 |

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.guildLevel` | number | 1 | 현재 길드 레벨 (1~10) |
| `A.guildXp` | number | 0 | 현재 레벨 내 XP |
| `A.guildTotalXp` | number | 0 | 누적 총 XP |
| `A.unlockedIds` | string[] | [] | 구매 완료 해금 ID |
| `A.focusStreakCount` | number | 0 | 연속 집중 횟수 (자정 리셋) |

### 3.2. 상수 (Constants)

**LEVEL_XP** — 레벨별 다음 레벨까지 필요 XP (index = level):

```
[0, 0, 50, 100, 180, 280, 400, 550, 720, 900, 1100]
```

| 레벨 | 필요 XP |
|------|---------|
| 1 → 2 | 50 |
| 2 → 3 | 100 |
| 3 → 4 | 180 |
| 4 → 5 | 280 |
| 5 → 6 | 400 |
| 6 → 7 | 550 |
| 7 → 8 | 720 |
| 8 → 9 | 900 |
| 9 → 10 | 1100 |

**MAX_LEVEL** = 10

**GUILD_TITLES** — 레벨별 칭호:

| 레벨 | 칭호 |
|------|------|
| 1 | 견습 길드마스터 |
| 2 | 초보 길드마스터 |
| 3 | 정식 길드마스터 |
| 4 | 숙련 길드마스터 |
| 5 | 노련한 길드마스터 |
| 6 | 전략가 길드마스터 |
| 7 | 원로 길드마스터 |
| 8 | 대길드마스터 |
| 9 | 전설의 길드마스터 |
| 10 | 만고불변의 길드마스터 |

**RARITY_BY_LEVEL** — 레벨별 확률 가중치 브래킷 (길드원 모집 등에서 사용)

## 4. 동작 규칙

### 4.1. XP 획득 경로

| 행동 | XP | 조건 | 코드 위치 |
|------|-----|------|----------|
| 일반 퀘스트 완료 | +10 | `recurrence='none'` | `toggleInstanceDone` |
| 반복 퀘스트 인스턴스 완료 | +5 | recurring instance | `toggleInstanceDone` |
| Focus 세션 완료 (>=25분) | +15 | `focusDuration >= 25` | `onPhaseEnd` |
| Focus 3연속 보너스 | +20 | `focusStreakCount % 3 === 0` | `onPhaseEnd` |
| 스톱워치 세션 (>=25분) | +15 | `swElapsed >= 25 * 60` | `stopSW` |

### 4.2. 레벨업 처리 (`gainXp` -> `onLevelUp`)

1. `A.guildXp += amount`
2. While `guildXp >= LEVEL_XP[nextLevel]`: subtract required XP, level++, call `onLevelUp`
3. `onLevelUp`: toast 알림, auto-apply 해금(해당 레벨), rarity weights 적용, level-up 애니메이션 트리거
4. `updateGuildLevelDisplay`: DOM 요소 갱신 (레벨, 칭호, XP 바)

### 4.3. 자정 리셋

| 속성 | 리셋 값 |
|------|---------|
| `focusStreakCount` | 0 |
| `questGoldToday` | 0 |

### 4.4. 함수 매핑

| 함수 | 역할 |
|------|------|
| `gainXp(amount)` | XP 추가, 레벨업 체크, UI 갱신 |
| `onLevelUp(newLevel)` | 레벨업 처리 (auto 해금, 확률 보정, 애니메이션) |
| `applyRarityWeights(level)` | `RARITY_BY_LEVEL`에서 현재 레벨 bracket 적용 |
| `updateGuildLevelDisplay()` | 레벨·칭호·XP 바 DOM 갱신 |
| `triggerLevelUpAnimation()` | sigil에 `level-up-flash` 클래스 1.5초 적용 |

## 5. 사용자 상호작용

### 5.1. 조작 방법

- XP는 퀘스트 완료 / 집중 세션 완료 시 **자동 획득** (별도 조작 불요)
- 레벨업 시 toast 알림이 자동 표시됨
- 해금 모달에서 레벨별 진행 상황 확인 가능

### 5.2. 키보드 단축키

해당 없음.

### 5.3. 이벤트 흐름

**퀘스트 완료 -> XP 획득 시나리오**:

1. 사용자가 퀘스트 체크박스 클릭
2. `toggleInstanceDone` 호출
3. 퀘스트 타입에 따라 `gainXp(10)` 또는 `gainXp(5)` 호출
4. 레벨업 조건 충족 시 `onLevelUp` -> toast + 애니메이션
5. `updateGuildLevelDisplay`로 XP 바·레벨·칭호 갱신

**Focus 세션 완료 -> XP 획득 시나리오**:

1. 타이머/스톱워치 세션 종료 (25분 이상)
2. `gainXp(15)` 호출
3. `focusStreakCount` 증가, 3의 배수이면 추가 `gainXp(20)`
4. 레벨업 조건 충족 시 `onLevelUp` -> toast + 애니메이션

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `unlock-shop` | 레벨에 따른 해금 요소 관리 |
| `gold-economy` | 퀘스트 골드 상한이 레벨에 따라 증가 |
| `guild-member-recruit` | 모집 확률이 레벨에 따라 변동 |
| `guild-info` | XP 바·레벨·칭호 표시 |
| `focus-panel` | 집중 세션 완료 시 XP 획득 |
| `quests-panel` | 퀘스트 완료 시 XP 획득 |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-26 | 아이디어 단계 초안 작성 (draft). |
| 2026-04-27 | HTML 구현 완료에 따라 승격. 전체 스펙 문서화. |

# Experience System — 경험치 시스템

> **문서 성격**: `experience` 시스템의 **경험치** 스펙.
> 길드 레벨 1~99 성장 시스템. 퀘스트 완료·집중 세션으로 XP를 획득하고, XP+골드 충족 시 수동 레벨업하여 해금 요소를 활성화한다.
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

- **한 줄 정의**: 길드 레벨 1~99 성장 시스템. XP+골드 충족 시 수동 레벨업, 카테고리별 기능 해금 활성화.
- **위치**: guild-info 패널 (XP 바), 해금 모달
- **구현 상태**: ✅ 구현 완료

사용자가 퀘스트를 완료하거나 집중 세션(타이머/스톱워치)을 마치면 XP를 획득한다. 누적 XP와 골드가 레벨업 요구치를 충족하면 해금 모달에서 수동으로 레벨업 버튼을 클릭하여 길드 레벨을 상승시킨다. 레벨에 따라 모집 확률이 보정되고, 기능 해금 구매가 가능해진다. 길드 레벨은 1에서 시작하여 최대 99까지 성장하며(디자인 캡 Lv.20), 레벨마다 고유 칭호가 부여된다.

## 2. UI 구조

### 2.1. 와이어프레임

**guild-info 패널 내 XP 표시**

```
┌────────────────────────────────┐
│  [G]  길드명                    │
│       Lv.3  ████████░░░░  64%  │  ← XP bar (3px, gold gradient)
│       "정식 길드마스터"          │  ← 칭호
│       120 / 196 XP             │  ← XP 텍스트 (DM Mono 8px)
└────────────────────────────────┘
```

**해금 모달 내 레벨업 UI**

```
┌─────────────────────────────────────────────┐
│ ⚜ 길드 레벨                                │
│ ┌─────────────────────────────────────────┐ │
│ │ Lv.3 정식 길드마스터    [레벨업 (1,500G)]│ │
│ │                                         │ │
│ │ EXP  ████████████░░░░  120 / 196        │ │
│ │ GOLD ██████████████░░  8,000 / 1,500    │ │
│ │                                         │ │
│ │ 현재 모집 확률                           │ │
│ │ [Normal 60%] [Rare 35%] [Epic 5%] [Leg 0%]│
│ └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.guild-xp-wrap
  └ .guild-xp-bar          ← XP 진행 바 (3px, gold gradient)
.guild-xp-text              ← XP 수치 텍스트 (DM Mono 8px)
.sigil-letter.level-up-flash ← 레벨업 시 sigil flash 애니메이션
.unlock-level-card           ← 해금 모달 내 길드 레벨 카드
  .unlock-level-progress     ← EXP/GOLD 진행 바
  .unlock-level-rarity       ← 모집 확률 표시
  .unlock-lvlup-btn          ← 레벨업 버튼 (.ready / .disabled / .maxed)
```

### 2.3. 시각 요소 상세

| 요소 | 속성 |
|------|------|
| XP 바 | 높이 3px, gold gradient 배경 |
| XP 텍스트 | DM Mono, 8px |
| 레벨업 애니메이션 | sigil에 `level-up-flash` 클래스 적용, 1.5초 후 제거 |
| 레벨업 버튼 (ready) | gold gradient, box-shadow |
| 레벨업 버튼 (disabled) | surface3, muted, 부족 사유 tooltip |

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.guildLevel` | number | 1 | 현재 길드 레벨 (1~99) |
| `A.guildXp` | number | 0 | 현재 누적 XP |
| `A.unlockedIds` | string[] | [] | 구매 완료 해금 ID |
| `A.focusStreakCount` | number | 0 | 연속 집중 횟수 (자정 리셋) |

### 3.2. 상수 (Constants)

**MAX_LEVEL** = 99 (무한 성장, 시스템 상한)

**MAX_DESIGN_LEVEL** = 20 (해금/효과가 멈추는 디자인 캡)

**레벨업 필요 XP** — 지수 공식: `Math.floor(100 × 1.4^(level-1))`

| 레벨 | 필요 XP | 누적 XP |
|------|---------|---------|
| 1 → 2 | 100 | 100 |
| 2 → 3 | 140 | 240 |
| 3 → 4 | 196 | 436 |
| 4 → 5 | 274 | 710 |
| 5 → 6 | 384 | 1,094 |
| 6 → 7 | 538 | 1,632 |
| 7 → 8 | 753 | 2,385 |
| 8 → 9 | 1,054 | 3,439 |
| 9 → 10 | 1,476 | 4,915 |
| 10 → 11 | 2,066 | 6,981 |
| … | 지수 증가 | … |
| 19 → 20 | 17,192 | ~73,000 |

**레벨업 골드 비용** — 선형 공식: `500 × level`

| 레벨 | 골드 비용 |
|------|-----------|
| 1 → 2 | 500G |
| 5 → 6 | 2,500G |
| 10 → 11 | 5,000G |
| 20 → 21 | 10,000G |

**GUILD_TITLES_V2** — 레벨별 칭호 (21개, 디자인 캡 Lv.20):

| 레벨 | 칭호 |
|------|------|
| 1~2 | 견습 길드마스터 |
| 3~4 | 초보 길드마스터 |
| 5~6 | 정식 길드마스터 |
| 7~8 | 숙련 길드마스터 |
| 9~10 | 노련한 길드마스터 |
| 11~12 | 전략가 길드마스터 |
| 13~14 | 원로 길드마스터 |
| 15~17 | 대길드마스터 |
| 18~20 | 전설의 길드마스터 |
| 20+ | 만고불변의 길드마스터 |

**XP_REWARDS** — 행동별 XP 적립:

| 상수 키 | XP | 설명 |
|---------|-----|------|
| `stopwatchPerMin` | 10 | 스톱워치 분당 XP |
| `timerComplete` | 20 | 타이머 완료 시 |
| `questComplete` | 50 | 퀘스트 완료 시 |

**RARITY_BY_LEVEL_V2** — 레벨별 모집 확률 가중치 (별도 문서: `recruit-probability-table`)

## 4. 동작 규칙

### 4.1. XP 획득 경로

| 행동 | XP | 조건 | 코드 위치 |
|------|-----|------|----------|
| 스톱워치 1분 경과 | +10 | 가동 중 매 분 | `XP_REWARDS.stopwatchPerMin` |
| 타이머 완료 | +20 | 타이머 세션 완료 시 | `XP_REWARDS.timerComplete` |
| 퀘스트 완료 | +50 | 퀘스트 체크 시 | `XP_REWARDS.questComplete` |
| Focus 세션 (>=25분) | +15 | `focusDuration >= 25` | `onPhaseEnd` |
| Focus 3연속 보너스 | +20 | `focusStreakCount % 3 === 0` | `onPhaseEnd` |

### 4.2. 레벨업 처리 (`tryGuildLevelUp`)

v2에서는 **수동 레벨업** 방식:

1. 해금 모달에서 "레벨업" 버튼 클릭
2. EXP 충족 확인: `A.guildXp >= levelUpXp(A.guildLevel)`
3. 골드 충족 확인: `A.gold >= levelUpGoldCost(A.guildLevel)`
4. 부족 시 toast 알림 + 버튼 disabled 상태
5. 충족 시:
   - `A.guildXp -= need`, `A.gold -= goldCost`
   - `A.guildLevel++`
   - 확률 보정 적용 (`applyRarityWeightsByLevel`)
   - UI 갱신 (`updateGoldDisplay`, `updateGuildLevelDisplay`, `renderUnlockShop`)
   - toast: "길드 레벨 N 달성! — 칭호"
   - 레벨업 애니메이션 트리거

### 4.3. XP 축적 (`gainXp`)

1. `A.guildXp += amount` (레벨업 조건 체크 없이 단순 축적)
2. `updateGuildLevelDisplay()` 호출
3. 해금 모달이 열려있으면 `renderUnlockShop()` 갱신

### 4.4. 자정 리셋

| 속성 | 리셋 값 |
|------|---------|
| `focusStreakCount` | 0 |
| `questGoldToday` | 0 |

### 4.5. 함수 매핑

| 함수 | 역할 |
|------|------|
| `gainXp(amount)` | XP 축적, UI 갱신 (자동 레벨업 없음) |
| `tryGuildLevelUp()` | 수동 레벨업 처리 (EXP+골드 충족 확인 → 레벨업) |
| `levelUpXp(level)` | 레벨업 필요 XP 계산: `Math.floor(100 * 1.4^(level-1))` |
| `levelUpGoldCost(level)` | 레벨업 골드 비용 계산: `500 * level` |
| `getGuildTitle(level)` | 디자인 레벨 기준 칭호 반환 |
| `applyRarityWeightsByLevel(level)` | `RARITY_BY_LEVEL_V2`에서 현재 레벨 bracket 적용 |
| `getRarityWeights(level)` | 레벨에 맞는 확률 가중치 배열 반환 |
| `updateGuildLevelDisplay()` | 레벨·칭호·XP 바 DOM 갱신 |
| `triggerLevelUpAnimation()` | sigil에 `level-up-flash` 클래스 1.5초 적용 |

## 5. 사용자 상호작용

### 5.1. 조작 방법

- XP는 퀘스트 완료 / 집중 세션 완료 시 **자동 획득** (별도 조작 불요)
- 레벨업은 해금 모달에서 **수동 클릭** (EXP + 골드 충족 필요)
- 해금 모달에서 레벨별 진행 상황, 현재 모집 확률, 부족 자원 확인 가능

### 5.2. 키보드 단축키

해당 없음.

### 5.3. 이벤트 흐름

**퀘스트 완료 -> XP 획득 시나리오**:

1. 사용자가 퀘스트 체크박스 클릭
2. `gainXp(50)` 호출
3. XP 바 갱신 (자동 레벨업 없음)
4. 사용자가 해금 모달에서 레벨업 버튼 클릭 시 레벨업 처리

**레벨업 시나리오**:

1. 해금 모달에서 EXP/GOLD 충족 확인
2. "레벨업 (N G)" 버튼 클릭
3. `tryGuildLevelUp()` → XP 차감 + 골드 차감 + 레벨 상승
4. toast + 애니메이션 + 확률 보정 + UI 갱신

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `unlock-shop` | 레벨에 따른 기능 해금, 레벨업 UI 제공 |
| `gold-economy` | 레벨업 골드 비용 소모처 |
| `guild-member-recruit` | 모집 확률이 레벨에 따라 변동 |
| `guild-info` | XP 바·레벨·칭호 표시 |
| `focus-panel` | 집중 세션 완료 시 XP 획득 |
| `quests-panel` | 퀘스트 완료 시 XP 획득 |
| `music-player` | 트랙 잠금 해금과 연동 (콘텐츠 해금) |
| `settings` | 배경화면 해금과 연동 (콘텐츠 해금) |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-26 | 아이디어 단계 초안 작성 (draft). |
| 2026-04-27 | HTML 구현 완료에 따라 승격. 전체 스펙 문서화. |
| 2026-05-06 | v2 스펙 반영. MAX_LEVEL 99, 지수 XP 곡선, 수동 레벨업(골드 비용), 칭호 21개, XP_REWARDS 상수. |

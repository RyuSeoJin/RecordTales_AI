# Unlock Shop (해금)

> **문서 성격**: `Guild` 시스템의 **Unlock Shop (해금)** UI 컴포넌트 스펙.
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

- **한 줄 정의**: 길드 레벨에 따라 자동 해금 또는 골드로 구매하여 기능을 확장하는 시스템
- **위치**: Command Post 내 "해금" 버튼 → 모달
- **구현 상태**: ✅ 구현 완료

---

## 2. UI 구조

### 2.1. 와이어프레임

모달 구성: 헤더(제목 + 레벨 + XP 바) → 탭(레벨 보상 / 상점) → 푸터(닫기 버튼).

**레벨 보상 탭**:

```
┌─────────────────────────────┐
│          해금                │
│   Lv.3 정식 길드마스터        │
│   [████████░░] XP: 80/100   │
│   [레벨 보상]  [상점]        │
├─────────────────────────────┤
│ Lv.2                        │
│  ✅ 모집 확률 보정 I    AUTO │
│  ✅ 길드 칭호          AUTO │
│  🔒 앰비언트 해금   1,500G  │
├─────────────────────────────┤
│ Lv.3  CURRENT               │
│  ✅ 길드원 보유 상한+2  AUTO │
│  ✅ 새 능력치 해금     AUTO │
│  ✅ 길드 칭호          AUTO │
├─────────────────────────────┤
│ Lv.4 🔒                     │
│  ...                        │
└─────────────────────────────┘
```

**상점 탭**:

```
┌───────┐ ┌───────┐ ┌───────┐
│  📋   │ │  📋   │ │  ⚔️   │
│프리셋 │ │프리셋 │ │편성슬롯│
│3→4    │ │4→5    │ │4→5    │
│Lv.4   │ │Lv.8   │ │Lv.5   │
│[3000G]│ │[🔒Lv8]│ │[5000G]│
└───────┘ └───────┘ └───────┘
```

### 2.2. CSS 클래스 구조

```
.unlock-modal
  .unlock-level-header
    .unlock-lvl-title
    .unlock-xp-bar-wrap > .unlock-xp-bar
    .unlock-xp-label
  .gm-tabs > .gm-tab
  .gm-tab-content
    #unlockTree
      .unlock-tree
        .unlock-level-block (.current | .completed | .locked)
          .unlock-level-label
          .unlock-item
            .unlock-item-info
              .unlock-item-name
              .unlock-item-detail
            .unlock-item-tag (.tag-auto | .tag-buy | .tag-purchased | .tag-locked)
    #unlockStore
      .unlock-store-grid
        .unlock-store-card (.available | .purchased | .level-locked)
          .unlock-store-icon
          .unlock-store-name
          .unlock-store-req
          .unlock-store-btn (.buyable | .bought | .locked)
```

### 2.3. 시각 요소 상세

| 요소 | 속성 |
|------|------|
| Modal | max-width: 640px |
| Level title | Cinzel 18px, `gold-soft` 색상 |
| XP bar | 6px height, gold gradient, max-width 320px |
| XP label | DM Mono 9px, `text-muted` 색상 |
| Level block | `surface2` 배경, 12px radius, 14px 16px padding |
| Current block | gold border glow |
| Locked block | opacity 0.5 |
| Store grid | 3 columns |
| Store card | `surface2` 배경, 12px radius, center-aligned |
| Tag `auto` | green-tinted |
| Tag `buy` | gold-tinted |
| Tag `purchased` | success-tinted |
| Tag `locked` | muted |

---

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.guildLevel` | number | 1 | 현재 길드 레벨 (1~10) |
| `A.guildXp` | number | 0 | 현재 레벨 내 XP |
| `A.unlockedIds` | string[] | [] | 구매 완료된 해금 ID 배열 |
| `A.gold` | number | 5000 | 보유 골드 |

### 3.2. 데이터 스키마

#### 3.2.1. UNLOCKS 상수 배열

13개 항목. 각 항목의 필드: `id`, `level`, `type`(`'auto'` | `'purchase'`), `cost`(purchase만), `desc`, `detail`.

**자동 해금(auto) — 6개**:

| ID | 레벨 | 효과 |
|----|------|------|
| `cap10` | 3 | 길드원 보유 상한 8→10 |
| `cap12` | 7 | 길드원 보유 상한 10→12 |
| `stat_xp` | 3 | 능력치 '경험치 보너스' 추가 |
| `stat_rest` | 6 | 능력치 '휴식 효율' 추가 |
| `goldcap2500` | 4 | 퀘스트 골드 일일 상한 2000→2500 |
| `goldcap3000` | 7 | 퀘스트 골드 일일 상한 2500→3000 |

**구매 해금(purchase) — 7개**:

| ID | 레벨 | 비용 | 효과 |
|----|------|------|------|
| `preset4` | 4 | 3,000G | 스쿼드 프리셋 3→4 |
| `preset5` | 8 | 5,000G | 스쿼드 프리셋 4→5 |
| `squad5` | 5 | 5,000G | 편성 슬롯 4→5 |
| `squad6` | 9 | 8,000G | 편성 슬롯 5→6 |
| `amb_thunder` | 2 | 1,500G | 앰비언트 Thunder ⚡ |
| `amb_cave` | 5 | 2,000G | 앰비언트 Cave 🦇 |
| `amb_tavern` | 8 | 3,000G | 앰비언트 Tavern 🍺 |

#### 3.2.2. 레벨 연동 보상 (비-UNLOCKS)

UNLOCKS 배열 외에 레벨별로 자동 적용되는 보상:

- 레어리티 가중치 조정 — Lv.2, 4, 6, 8 (`RARITY_BY_LEVEL` 참조)
- 길드 칭호 변경 — 매 레벨 (`GUILD_TITLES` 참조)

---

## 4. 동작 규칙

### 4.1. 해금 분류

두 가지 타입:

- **자동 해금(auto)**: 레벨 도달 시 `onLevelUp()`에서 자동 적용
- **구매 해금(purchase)**: 레벨 조건 충족 + 골드 소모

### 4.2. 구매 흐름

1. 레벨 조건 확인 (`guildLevel >= unlock.level`)
2. 이미 구매 여부 확인 (`unlockedIds`에 포함?)
3. 골드 잔액 확인 (`gold >= cost`)
4. 골드 차감 → `applyUnlock()` → `unlockedIds`에 추가
5. UI 갱신 (`updateGoldDisplay`, `renderUnlockShop`)

### 4.3. applyUnlock 효과

`unlock.id` 기준 switch 분기:

| ID | 효과 |
|----|------|
| `cap10` / `cap12` | `A.guildMemberCap` 변경 |
| `stat_xp` / `stat_rest` | `STAT_POOL`에 새 능력치 추가 |
| `goldcap2500` / `goldcap3000` | `A.questGoldDailyCap` 변경 |
| `preset4` / `preset5` | `PRESET_COUNT` 증가 + `squadPresets` 배열 확장 |
| `squad5` / `squad6` | `SQUAD_SLOTS` 변경 |
| `amb_*` | DOM에 새 앰비언트 사운드 트랙 추가 |

### 4.4. 함수 매핑

| 함수 | 역할 |
|------|------|
| `openUnlockShop()` | 해금 모달 열기, tree 탭 기본 |
| `closeUnlockShop()` | 모달 닫기 |
| `switchUnlockTab(tab)` | 레벨 보상/상점 탭 전환 |
| `renderUnlockShop()` | 헤더 + 활성 탭 렌더링 |
| `renderUnlockHeader()` | 레벨·XP 바 헤더 갱신 |
| `renderUnlockTree()` | 레벨별 해금 트리 목록 |
| `renderUnlockStore()` | 구매 가능 아이템 그리드 |
| `purchaseUnlock(id)` | 구매 처리 (검증→차감→적용→UI갱신) |
| `applyUnlock(u)` | 해금 효과 실행 (switch 분기) |
| `expandPresets(n)` | 프리셋 배열 확장 |
| `addStatIfMissing(name, unit)` | `STAT_POOL`에 중복 없이 추가 |
| `addAmbientSound(id, name, icon)` | DOM에 앰비언트 트랙 추가 |

---

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 결과 |
|------|------|
| Command Post "해금" 클릭 | 해금 모달 열림, 레벨 보상 탭 표시 |
| "레벨 보상" 탭 클릭 | 레벨별 해금 트리 표시 |
| "상점" 탭 클릭 | 구매 가능 아이템 그리드 표시 |
| 트리 내 골드 버튼 클릭 | `purchaseUnlock` 실행 |
| 상점 카드 구매 버튼 클릭 | `purchaseUnlock` 실행 |
| "닫기" 클릭 | 모달 닫힘 |

### 5.2. 키보드 단축키

해당 없음.

### 5.3. 구매 실패 시 피드백

| 조건 | Toast 메시지 |
|------|-------------|
| 골드 부족 | "골드가 부족합니다" |
| 레벨 부족 | "레벨이 부족합니다" |
| 이미 구매 | "이미 해금되었습니다" |

---

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `experience-system` | 길드 레벨이 해금 조건 결정 |
| `gold-economy` | 구매 해금의 골드 소모처 |
| `command-post` | 해금 모달 진입점 |
| `ambient-sound` | 해금으로 앰비언트 사운드 추가 |
| `squad-formation` | 해금으로 프리셋·슬롯 확장 |
| `guild-member-recruit` | 해금으로 보유 상한·능력치 확장 |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-27 | 초안 작성. HTML 구현 기준 전체 스펙 문서화. |

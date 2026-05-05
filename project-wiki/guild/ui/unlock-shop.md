# Unlock Shop (해금) — v2

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

- **한 줄 정의**: 길드 레벨 카드 + 카테고리별 기능 해금을 단일 패널에서 관리하는 시스템
- **위치**: Command Post 내 "해금" 버튼 → 모달
- **구현 상태**: ✅ 구현 완료

v2에서는 트리/상점 탭 구조를 폐지하고, 단일 패널에 길드 레벨 카드와 5개 카테고리별 기능 해금 카드를 표시한다. 콘텐츠 해금(곡/배경)은 각각 음악 라이브러리와 환경 설정 모달로 이동했다.

---

## 2. UI 구조

### 2.1. 와이어프레임

```
┌──────────────────────────────────────────────┐
│                    해금                       │
│                                              │
│ ⚜ 길드 레벨                                  │
│ ┌──────────────────────────────────────────┐ │
│ │ Lv.3 정식 길드마스터    [레벨업 (1,500G)] │ │
│ │                                          │ │
│ │ EXP  ████████░░░  120 / 196              │ │
│ │ GOLD ██████████░  8,000 / 1,500          │ │
│ │                                          │ │
│ │ 현재 모집 확률                            │ │
│ │ [Normal 60%] [Rare 35%] [Epic 5%] [Leg 0%]│
│ │ 다음 레벨에서: [55%] [37%] [7%] [1%]      │ │
│ └──────────────────────────────────────────┘ │
│                                              │
│ 🔓 기능 해금                                  │
│ ┌──────────────────────────────────────────┐ │
│ │ 📋 스쿼드 프리셋  프리셋 1개 → 2개 [8,000G]│ │
│ │    전투 진형을 여러 세트로 저장   ●○ 1/2단계│ │
│ ├──────────────────────────────────────────┤ │
│ │ ⚔️ 편성 슬롯      슬롯 2개 → 3개 [15,000G]│ │
│ │    동시에 출전 가능한 길드원 수   ●○ 1/2단계│ │
│ ├──────────────────────────────────────────┤ │
│ │ ⏱ 스톱워치 보상   +0% → +20%    [3,000G] │ │
│ │    스톱워치 완료 시 골드 배수    ○○○○○     │ │
│ ├──────────────────────────────────────────┤ │
│ │ ⏲ 타이머 보상     +0% → +20%    [3,000G] │ │
│ │    타이머 완료 시 골드 배수      ○○○○○     │ │
│ ├──────────────────────────────────────────┤ │
│ │ 📜 퀘스트 보상     +0% → +20%    [3,000G] │ │
│ │    퀘스트 달성 시 골드 배수      ○○○○○     │ │
│ └──────────────────────────────────────────┘ │
│                                              │
│ [DEV] +50K 골드  +500 EXP  +5K EXP          │
│                                    [닫기]    │
└──────────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.unlock-modal (max-width:780px)
  .hire-title ("해금")
  #unlockTabFeatures
    .unlock-section > .unlock-section-label ("⚜ 길드 레벨")
      .unlock-level-card
        .unlock-level-card-head
          .unlock-level-card-title + .unlock-level-card-sub
          .unlock-lvlup-btn (.ready | .disabled | .maxed)
        .unlock-level-progress
          .unlock-level-progress-row (×2: EXP, GOLD)
            .unlock-level-progress-label (.ready)
            .unlock-level-bar > .unlock-level-bar-fill (.xp | .gold)
            .unlock-level-progress-val
        .unlock-level-rarity
          .unlock-level-rarity-bars > .rar-bar (×4)
          .unlock-level-rarity-next > .rar-pill (×4)
    .unlock-section > .unlock-section-label ("🔓 기능 해금")
      .unlock-cat-card (.maxed) (×5 카테고리)
        .unlock-cat-card-head
          .unlock-cat-icon
          .unlock-cat-card-titles > .unlock-cat-title + .unlock-cat-desc
          .u-item-btn (.buy | .bought | .poor | .locked)
        .unlock-cat-card-body
          .u-cat-effect-line (현재 → 다음)
          .u-cat-progress > .u-cat-dots + .u-cat-progress-label
  .gm-modal-footer-with-dev
    .gm-dev-tools > .gm-dev-btn (DEV 버튼들)
    .btn-secondary ("닫기")
```

### 2.3. 시각 요소 상세

| 요소 | 속성 |
|------|------|
| Modal | max-width: 780px |
| 레벨 카드 | gold gradient 배경, 14px radius |
| EXP 바 | blue-purple gradient, box-shadow |
| GOLD 바 | gold gradient, box-shadow |
| Ready 라벨 | gold-soft 색상 (충족 시) |
| 레벨업 버튼 ready | gold gradient, box-shadow, hover lift |
| 레벨업 버튼 disabled | surface3, muted, tooltip 표시 |
| 레벨업 버튼 maxed | orange tint |
| 확률 바 | 등급별 색상 (normal/rare/epic/legendary) |
| 카테고리 카드 | surface2 배경, 12px radius |
| 카테고리 maxed | green tint 배경 |
| 진척도 도트 filled | gold-soft, box-shadow |
| 효과 화살표 | muted → gold-soft |
| DEV 버튼 | surface3, tiny text |

---

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.guildLevel` | number | 1 | 현재 길드 레벨 (1~99) |
| `A.guildXp` | number | 0 | 현재 누적 XP |
| `A.unlockedIds` | string[] | [] | 구매 완료된 해금 ID 배열 |
| `A.gold` | number | 5000 | 보유 골드 |

### 3.2. 데이터 스키마

#### 3.2.1. UNLOCKS_V2 상수 배열

20개 항목. 5개 카테고리, 각 항목의 필드: `id`, `cat`, `cost`, `desc`, `detail`, `tier`(optional), `requires`(optional).

**스쿼드 프리셋 (preset) — 2개**:

| ID | 비용 | 효과 | 선행 조건 |
|----|------|------|-----------|
| `preset2` | 8,000G | 프리셋 1→2 | — |
| `preset3` | 25,000G | 프리셋 2→3 | preset2 |

**편성 슬롯 (squad) — 2개**:

| ID | 비용 | 효과 | 선행 조건 |
|----|------|------|-----------|
| `squad3` | 15,000G | 슬롯 2→3 | — |
| `squad4` | 50,000G | 슬롯 3→4 | squad3 |

**스톱워치 보상 (stopwatch) — 5개** (각 +20%, 비용 점증):

| ID | 비용 | 효과 | 선행 조건 |
|----|------|------|-----------|
| `stopwatch1` | 3,000G | +0%→+20% | — |
| `stopwatch2` | 8,000G | +20%→+40% | stopwatch1 |
| `stopwatch3` | 18,000G | +40%→+60% | stopwatch2 |
| `stopwatch4` | 35,000G | +60%→+80% | stopwatch3 |
| `stopwatch5` | 60,000G | +80%→+100% | stopwatch4 |

**타이머 보상 (timer) — 5개**: 스톱워치와 동일 구조 (timer1~timer5)

**퀘스트 보상 (quest) — 5개**: 스톱워치와 동일 구조 (quest1~quest5)

#### 3.2.2. CONTENT_UNLOCKS 상수 배열

**곡 해금 (track) — 4개**:

| ID | 비용 | TRACKS 인덱스 | 곡명 |
|----|------|---------------|------|
| `track2` | 3,000G | 3 | Torchlit Library |
| `track3` | 5,000G | 5 | Forge of Stars |
| `track4` | 8,000G | 2 | Moonlit Vow |
| `track5` | 12,000G | 4 | Whispers of the Arch |

**배경화면 해금 (bg) — 8개**:

| ID | 비용 | 이름 |
|----|------|------|
| `bg1` | 2,000G | Misty Forest |
| `bg2` | 3,000G | Ancient Library |
| `bg3` | 5,000G | Stone Keep |
| `bg4` | 7,000G | Starry Night |
| `bg5` | 10,000G | Mountain Pass |
| `bg6` | 14,000G | Royal Throne Room |
| `bg7` | 20,000G | Dragon's Lair |
| `bg8` | 30,000G | Celestial Realm |

---

## 4. 동작 규칙

### 4.1. 해금 분류

v2에서는 **requires 체인** 기반 카테고리별 순차 해금:

- 각 카테고리 내에서 `requires` 필드로 선행 조건 체인 구성
- 카테고리의 "다음 구매 항목"은 첫 번째 미구매 항목
- 카테고리 전체 구매 시 "최대" 상태 표시

### 4.2. 구매 흐름

1. 선행 조건 확인 (`requires`에 해당하는 ID가 `unlockedIds`에 포함?)
2. 이미 구매 여부 확인 (`unlockedIds`에 포함?)
3. 골드 잔액 확인 (`gold >= cost`)
4. 골드 차감 → `applyUnlockV2(id)` → `unlockedIds`에 추가
5. UI 갱신 (`updateGoldDisplay`, `renderUnlockShop`)
6. 길드 관리 모달이 열려있으면 `renderFormation` + `renderMembersList` 갱신

### 4.3. applyUnlockV2 효과

`unlock.id` 기준 switch 분기:

| ID | 효과 |
|----|------|
| `preset2` / `preset3` | `getActivePresetCount()` → `PRESET_COUNT` + `squadPresets` 배열 확장 |
| `squad3` / `squad4` | `getActiveSquadSlots()` → `SQUAD_SLOTS` 변경 |
| 보상 관련 (stopwatch/timer/quest) | 별도 적용 없음 — `getRewardMultiplier(category)`가 동적으로 읽음 |

### 4.4. 레벨업 (tryGuildLevelUp)

1. MAX_LEVEL 도달 시 toast "이미 만렙입니다"
2. EXP 부족 시 toast + 부족량 안내
3. 골드 부족 시 toast + 부족량 안내
4. 충족 시: XP 차감, 골드 차감, 레벨++, 확률 보정, UI 갱신

### 4.5. 헬퍼 함수

| 함수 | 역할 |
|------|------|
| `getActivePresetCount()` | `unlockedIds` 기반 현재 프리셋 수 (1/2/3) |
| `getActiveSquadSlots()` | `unlockedIds` 기반 현재 슬롯 수 (2/3/4) |
| `getRewardMultiplier(cat)` | 카테고리별 보상 배수 (1.0 ~ 2.0) |
| `isTrackUnlocked(idx)` | 트랙 해금 여부 (0,1번은 기본 제공) |
| `getCategoryProgress(catKey)` | 카테고리 진척도 {current, total, items} |
| `getNextUnlockInCategory(catKey)` | 다음 구매 항목 (null = 만렙) |
| `getCurrentEffectText(catKey)` | 현재 효과 텍스트 |
| `getNextEffectText(catKey)` | 다음 효과 텍스트 |

### 4.6. 함수 매핑

| 함수 | 역할 |
|------|------|
| `openUnlockShop()` | 해금 모달 열기 |
| `closeUnlockShop()` | 모달 닫기 |
| `renderUnlockShop()` | `renderFeaturesTab()` 호출 |
| `renderFeaturesTab()` | 길드 레벨 카드 + 기능 해금 카드 렌더링 |
| `renderGuildLevelCard()` | 레벨/XP/골드 진행바 + 확률 표시 |
| `renderFunctionalUnlocks()` | 5개 카테고리 카드 렌더링 |
| `purchaseUnlockV2(id)` | 구매 처리 (검증→차감→적용→UI갱신) |
| `applyUnlockV2(id)` | 해금 효과 실행 |
| `tryGuildLevelUp()` | 수동 레벨업 처리 |
| `devGainGold(amount)` | DEV: 골드 직접 지급 |
| `devGainXp(amount)` | DEV: EXP 직접 지급 |

---

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 결과 |
|------|------|
| Command Post "해금" 클릭 | 해금 모달 열림 |
| 레벨업 버튼 클릭 | EXP+골드 충족 시 레벨업, 부족 시 toast |
| 카테고리 구매 버튼 클릭 | `purchaseUnlockV2` 실행 |
| DEV +50K 골드 클릭 | 골드 50,000 추가 |
| DEV +500 EXP 클릭 | EXP 500 추가 |
| DEV +5K EXP 클릭 | EXP 5,000 추가 |
| "닫기" 클릭 | 모달 닫힘 |

### 5.2. 키보드 단축키

| 키 | 동작 |
|----|------|
| Escape | 모달 닫기 |

### 5.3. 구매 실패 시 피드백

| 조건 | Toast 메시지 |
|------|-------------|
| 골드 부족 | "골드가 N G 부족합니다" |
| 선행 조건 미충족 | "먼저 '선행 항목'을(를) 해금하세요" |
| 이미 구매 | "이미 N 해금됨" |
| EXP 부족 (레벨업) | "EXP가 N 부족합니다" |
| 골드 부족 (레벨업) | "골드가 N G 부족합니다" |

---

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `experience-system` | 길드 레벨이 확률 보정 결정, 레벨업 UI 제공 |
| `gold-economy` | 구매 해금 + 레벨업의 골드 소모처 |
| `command-post` | 해금 모달 진입점 |
| `squad-formation` | 해금으로 프리셋·슬롯 확장 |
| `music-player` | 콘텐츠 해금으로 곡 잠금 해제 (음악 라이브러리에서 구매) |
| `settings` | 콘텐츠 해금으로 배경화면 잠금 해제 (환경 설정에서 구매) |
| `guild-member-recruit` | 모집 확률이 레벨에 따라 변동 |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-27 | 초안 작성. HTML 구현 기준 전체 스펙 문서화. |
| 2026-05-06 | v2 전면 개편. 트리/상점 → 단일 패널, 카테고리별 기능해금, 콘텐츠해금(곡/배경) 분리, 수동 레벨업, DEV 도구. |

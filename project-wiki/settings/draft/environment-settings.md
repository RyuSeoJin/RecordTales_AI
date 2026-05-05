# Environment Settings — 환경 설정

> **문서 성격**: `settings` 시스템의 **환경 설정** 스펙.
> Command Post의 환경 설정 버튼(`openSettingsModal`)으로 접근하는 배경화면 설정 등 환경 관련 기능.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 목차

1. [개요](#1-개요)
2. [UI 구조](#2-ui-구조)
3. [데이터 모델](#3-데이터-모델)
4. [동작 규칙](#4-동작-규칙)
5. [사용자 상호작용](#5-사용자-상호작용)
6. [관련 시스템](#6-관련-시스템)

---

## 1. 개요

- **한 줄 정의**: 배경화면 해금·적용 등 환경 설정 기능을 제공하는 모달
- **위치**: Command Post → 환경 설정 버튼 (`openSettingsModal()`)
- **구현 상태**: ✅ 구현 완료 (배경화면 탭)

---

## 2. UI 구조

### 2.1. 와이어프레임

```
┌─ Settings Modal (.settings-modal-wrap) ───────────────────┐
│                                                            │
│  [배경화면]  [향후 탭...]          ← 탭 바                 │
│                                                            │
│  ┌─ .bg-grid ────────────────────────────────────────────┐ │
│  │                                                        │ │
│  │  ┌─ bg-card ──────┐  ┌─ bg-card ──────┐              │ │
│  │  │ [프리뷰 영역]  │  │ [프리뷰 영역]  │              │ │
│  │  │ (gradient/    │  │ (gradient)     │              │ │
│  │  │  default)     │  │                │              │ │
│  │  │ 적용중 뱃지?  │  │                │              │ │
│  │  ├────────────────┤  ├────────────────┤              │ │
│  │  │ 기본 배경      │  │ Misty Forest   │              │ │
│  │  │ Default Stage  │  │ 안개 낀 숲     │              │ │
│  │  │ [✓ 적용중]     │  │ [적용하기]     │              │ │
│  │  └────────────────┘  └────────────────┘              │ │
│  │                                                        │ │
│  │  ┌─ bg-card (잠금) ┐                                  │ │
│  │  │ [프리뷰 영역]  │                                  │ │
│  │  │ 🔒             │                                  │ │
│  │  ├────────────────┤                                  │ │
│  │  │ Stone Keep     │                                  │ │
│  │  │ 돌의 요새      │                                  │ │
│  │  │ [5,000G 해금]  │                                  │ │
│  │  └────────────────┘                                  │ │
│  └────────────────────────────────────────────────────────┘ │
│                                                            │
│                            [닫기]                          │
└────────────────────────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.settings-modal-wrap (.open)
└── .settings-modal
    ├── .settings-tab-bar > .settings-tab (.active)
    ├── .settings-tab-content #settingsTabBackground
    │   └── .bg-grid
    │       └── .bg-card (.applied)
    │           ├── .bg-card-preview (.bg-card-preview-default)
    │           │   └── .bg-card-applied-badge
    │           │   └── .bg-card-lock-icon
    │           └── .bg-card-info
    │               ├── .bg-card-name
    │               ├── .bg-card-hint
    │               └── .bg-card-btn (.applied | .apply | .buy | .poor)
    └── button.btn.btn-secondary (닫기)
```

### 2.3. 배경화면 카드 상태

| 상태 | 조건 | 버튼 | 동작 |
|------|------|------|------|
| 적용중 | `A.currentBgId === bg.id` | `✓ 적용중` (비활성) | — |
| 해금됨 | `A.unlockedIds.includes(bg.id)` | `적용하기` | `applyBackground(bg.id)` |
| 잠금 (골드 충분) | `!unlocked && A.gold >= bg.cost` | `{cost}G 해금` | `purchaseAndApplyBackground(bg.id)` |
| 잠금 (골드 부족) | `!unlocked && A.gold < bg.cost` | `{cost}G 해금` (dim) | 클릭 시 골드 부족 토스트 |

---

## 3. 데이터 모델

### 3.1. 전역 상태

| 변수 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.currentBgId` | `string|null` | `null` | 현재 적용된 배경 ID (null = 기본 배경) |
| `A.unlockedIds` | `string[]` | `[]` | 해금된 기능/콘텐츠 ID 목록 |
| `settingsActiveTab` | `string` | `'background'` | 현재 활성 탭 |

### 3.2. CONTENT_UNLOCKS (bg 카테고리)

| ID | 비용 | 이름 | 힌트 |
|----|------|------|------|
| bg1 | 2,000G | Misty Forest | 안개 낀 숲 |
| bg2 | 3,000G | Ancient Library | 고대 도서관 |
| bg3 | 5,000G | Stone Keep | 돌의 요새 |
| bg4 | 7,000G | Starry Night | 별이 빛나는 밤 |
| bg5 | 10,000G | Mountain Pass | 산악 고개 |
| bg6 | 14,000G | Royal Throne Room | 왕의 옥좌실 |
| bg7 | 20,000G | Dragon's Lair | 용의 둥지 |
| bg8 | 30,000G | Celestial Realm | 천상의 영역 |

각 배경은 `gradient` 속성으로 CSS gradient를 가지며, `.stage` 요소의 `background`에 적용된다.

---

## 4. 동작 규칙

### 4.1. 모달 제어

| 함수 | 역할 |
|------|------|
| `openSettingsModal()` | 모달 열기, 배경화면 탭으로 초기화, `renderSettingsModal()` 호출 |
| `closeSettingsModal()` | 모달 닫기 (`.open` 클래스 제거) |
| `switchSettingsTab(tab)` | 탭 전환 + `renderSettingsModal()` 호출 |
| `renderSettingsModal()` | 활성 탭에 따라 렌더링 분기 (현재: background만) |

### 4.2. 배경화면 설정

| 함수 | 역할 |
|------|------|
| `renderBackgroundSettings()` | 배경 카드 그리드 렌더링 (기본 + 해금 + 잠금 순) |
| `applyBackground(bgId)` | 배경 적용. `A.currentBgId` 갱신, `.stage` background 변경, 토스트 표시 |
| `purchaseAndApplyBackground(bgId)` | 골드 차감 → `unlockedIds`에 추가 → `applyBackground` 호출 |

### 4.3. 배경 복원

페이지 로드 시 `A.currentBgId`가 null이 아니면 `applyBackground(A.currentBgId)` 호출하여 저장된 배경을 복원한다.

---

## 5. 사용자 상호작용

| 액션 | 결과 |
|------|------|
| 환경 설정 버튼 클릭 | 설정 모달 열기 (배경화면 탭) |
| 기본 배경 적용 클릭 | `applyBackground(null)` — CSS 기본값 복원, 토스트 `🖼 기본 배경 적용` |
| 해금된 배경 적용 클릭 | `applyBackground(bgId)` — gradient 적용, 토스트 `🖼 {name} 적용` |
| 잠긴 배경 해금 클릭 (골드 충분) | `purchaseAndApplyBackground` — 골드 차감, 해금, 즉시 적용 |
| 잠긴 배경 해금 클릭 (골드 부족) | 골드 부족 토스트 표시 |
| 닫기 / Escape 키 | `closeSettingsModal()` |

---

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `command-post` | 환경 설정 버튼 진입점 (`openSettingsModal`) |
| `gold-economy` | 배경 해금 시 골드 소모 (CONTENT_UNLOCKS bg) |
| `unlock-shop` | 기능 해금과 동일한 `A.unlockedIds` 사용 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 (플레이스홀더). |
| 2026-05-06 | v2 구현 완료에 따라 전면 작성. 배경화면 설정 모달 (8종 + 기본), applyBackground/purchaseAndApplyBackground 함수, 카드 상태별 UX, 페이지 로드 복원 로직. |

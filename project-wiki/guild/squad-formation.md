# Squad Formation — 스쿼드 편성

> **문서 성격**: `guild` 시스템의 **스쿼드 편성** 스펙.
> 보유 길드원을 최대 4~6명 슬롯에 배치하여 스쿼드를 편성하고, 프리셋으로 여러 조합을 관리하는 시스템.
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

- **한 줄 정의**: 보유 길드원을 최대 4~6명 슬롯에 배치하여 스쿼드를 편성하고, 프리셋으로 여러 조합을 관리하는 시스템.
- **위치**: Command Post → 길드원 관리 → 편성 탭
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
[스쿼드1 (2/4)] [스쿼드2 (0/4)] [스쿼드3 (0/4)]   ← 프리셋 바 (멤버 수 표시)

Squad (2/4)
┌────┐ ┌────┐ ┌─ ─ ┐ ┌─ ─ ┐
│이미지│ │이미지│ │ +  │ │ +  │
│이름  │ │이름  │ │탭하여│ │탭하여│     ← 빈 슬롯: pulse 애니메이션
│★★   │ │★★★  │ │추가 │ │추가 │
│  ✕  │ │  ✕  │ │    │ │    │
└────┘ └────┘ └─ ─ ┘ └─ ─ ┘

┌─────────┬─────────┐
│집중 시간  │골드 획득  │    ← 스탯 합산
│  +17%   │  +10%   │
├─────────┼─────────┤
│퀘스트보상 │스톱워치   │
│   +0%   │  +8%    │
└─────────┴─────────┘

"아래 대기 길드원을 클릭하면 빈 슬롯에 편성됩니다"   ← 안내 텍스트

대기 길드원 (4)
┌────┐ ┌────┐ ┌────┐ ┌────┐
│이미지│ │이미지│ │이미지│ │이미지│
│ [+] │ │ [+] │ │ [+] │ │ [+] │   ← hover 시 + 오버레이
│이름  │ │이름  │ │이름  │ │이름  │
│집+5  │ │골+8  │ │퀘+12│ │스+6  │   ← 스탯 힌트 (축약)
└────┘ └────┘ └────┘ └────┘
```

> **UX 가이드라인**: 빈 슬롯에 `drop-target` pulse 애니메이션이 적용되어 "여기에 추가하세요"를 시각적으로 안내. 벤치 카드 hover 시 `+` 오버레이가 표시되어 클릭 가능함을 명시. 스쿼드가 가득 찬 경우 벤치 카드가 비활성화되고, 안내 텍스트가 "슬롯의 ✕를 눌러 길드원을 제거할 수 있습니다"로 전환.

### 2.2. CSS 클래스 구조

```
.gm-preset-bar > .gm-preset-btn (.active) > .gm-preset-dot (.filled|.empty)
.gm-squad-label
.gm-squad-slots > .gm-slot (.filled, .filling, .drop-target)
  > .gm-slot-img, .gm-slot-name, .gm-slot-rarity, .gm-slot-remove
  | .gm-slot-empty-icon, .gm-slot-empty-text
.gm-squad-summary > .gm-summary-item > .gm-summary-label + .gm-summary-value (.zero)
.gm-formation-hint
.gm-bench-label
.gm-bench (.highlight-mode) > .gm-bench-card > .gm-bench-img + .gm-bench-add + .gm-bench-name + .gm-bench-stat-hint
```

### 2.3. 시각 요소 상세

#### 프리셋 바 (`.gm-preset-bar`)

| 속성 | 값 |
|------|----|
| 배경 | `surface2` |
| 모서리 | `10px` radius |
| 구조 | segmented buttons |

#### 활성 프리셋 (`.gm-preset-btn.active`)

| 속성 | 값 |
|------|----|
| 배경 | `glass` |
| 텍스트 | `gold-soft` |
| 테두리 | `gold` border |

#### 프리셋 도트 (`.gm-preset-dot`)

| 속성 | 값 |
|------|----|
| 크기 | `6px` circle |
| filled | `gold-soft` |
| empty | `muted` 30% opacity |

#### 스쿼드 슬롯 (`.gm-slot`)

| 속성 | 값 |
|------|----|
| 너비 | `120px` |
| 비율 | `1160:1760` aspect ratio |
| 빈 슬롯 | dashed border |
| 채워진 슬롯 | solid border |
| hover (filled) | red border (remove hint) |

#### 제거 버튼 (`.gm-slot-remove`)

| 속성 | 값 |
|------|----|
| 크기 | `18px` circle |
| 표시 | hover 시 나타남 |
| 색상 | red tinted |

#### 스탯 요약 (`.gm-squad-summary`)

| 속성 | 값 |
|------|----|
| 레이아웃 | 2 columns grid |
| 카드 배경 | `surface2` |
| 수치 폰트 | `DM Mono 18px` |
| 수치 색상 | `gold-soft` (zero일 때 `muted` 50%) |

#### 벤치 (`.gm-bench`)

| 속성 | 값 |
|------|----|
| 레이아웃 | `flex wrap` |
| 카드 너비 | `80px` |
| 이미지 비율 | `1:1` aspect ratio |
| hover | blue border + `translateY(-2px)` + `+` 오버레이 표시 |
| 스쿼드 full 시 | `opacity: 0.5`, `pointer-events: none` (비활성화) |
| 하이라이트 | pulsing gold shadow animation (1s infinite) |

#### 벤치 카드 오버레이 (`.gm-bench-add`)

| 속성 | 값 |
|------|----|
| 배경 | `rgba(0,0,0,0.5)` |
| 표시 조건 | hover 시 `opacity: 0→1` |
| 내용 | `+` 아이콘 (16px, `rest-c` 색상) |

#### 벤치 스탯 힌트 (`.gm-bench-stat-hint`)

| 속성 | 값 |
|------|----|
| 폰트 | `DM Mono 7px` |
| 색상 | `gold-soft` |
| 내용 | 능력치 축약 (예: `집+5 골+8`) |

#### 안내 텍스트 (`.gm-formation-hint`)

| 속성 | 값 |
|------|----|
| 폰트 | `DM Mono 9px` |
| 색상 | `text-muted`, `opacity: 0.7` |
| 위치 | 스탯 요약 아래, 벤치 위 |
| 내용 (빈 슬롯) | "아래 대기 길드원을 클릭하면 빈 슬롯에 편성됩니다" |
| 내용 (full) | "슬롯의 ✕를 눌러 길드원을 제거할 수 있습니다" |

#### 애니메이션

| 이름 | 효과 |
|------|----|
| `@keyframes slotFill` | scale `0.8→1`, opacity `0.5→1` (0.35s) |
| `@keyframes slotPulse` | border-color 파란 펄스 (1.5s, 빈 슬롯용) |
| `@keyframes benchPulse` | gold box-shadow pulse (1s) |

## 3. 데이터 모델

### 3.1. 전역 상태 (`A`)

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.squadPresets` | `number[][]` | `[[],[],[]]` | 프리셋별 길드원 ID 배열 |
| `A.activePreset` | `number` | `0` | 현재 활성 프리셋 인덱스 |
| `A.squad` | `number[]` | `[]` | 활성 프리셋의 참조 (호환용) |

### 3.2. 상수

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `SQUAD_SLOTS` | `let number` | `4` | 현재 슬롯 수 (해금으로 5→6 확장) |
| `PRESET_COUNT` | `let number` | `3` | 현재 프리셋 수 (해금으로 4→5 확장) |

## 4. 동작 규칙

### 4.1. 프리셋 전환

- `switchPreset(idx)`: `activePreset` 변경, `squad` 참조 갱신, `renderFormation` 호출

### 4.2. 편성 추가/제거

- `addToSquad(id)`: 슬롯 가득 차면 toast, 중복 방지, 추가 후 `slotFill` 애니메이션
- `removeFromSquad(id)`: 배열에서 제거, 재렌더링

### 4.3. 스탯 합산

- `calcSquadStats(memberIds)`: `STAT_POOL` 기준으로 스쿼드 내 모든 멤버의 같은 이름 스탯을 합산

### 4.4. 벤치 하이라이트

- `highlightBench()`: 빈 슬롯 클릭 시 벤치 영역으로 스크롤 + 2초간 `benchPulse` 애니메이션

### 4.5. 함수 매핑

| 함수 | 역할 |
|------|------|
| `getActiveSquad()` | 활성 프리셋의 멤버 ID 배열 반환 |
| `setActiveSquad(arr)` | 활성 프리셋 갱신 + `A.squad` 동기화 |
| `switchPreset(idx)` | 프리셋 전환 + 렌더링 |
| `renderFormation()` | 전체 편성 UI 렌더링 |
| `addToSquad(id)` | 멤버 편성 추가 + 애니메이션 |
| `removeFromSquad(id)` | 멤버 편성 해제 |
| `calcSquadStats(ids)` | 스쿼드 스탯 합산 계산 |
| `highlightBench()` | 벤치 하이라이트 + 스크롤 |

## 5. 사용자 상호작용

| 액션 | 결과 |
|------|------|
| 프리셋 버튼 클릭 | 해당 프리셋으로 전환 (멤버 수 표시) |
| 벤치 길드원 클릭 | 스쿼드에 추가 (슬롯 여유 시). hover 시 `+` 오버레이로 클릭 가능 안내 |
| 벤치 길드원 (full 시) | 비활성 (opacity 50%, pointer-events off) |
| 슬롯 ✕ 버튼 클릭 | 스쿼드에서 제거 |
| 빈 슬롯 클릭 | 벤치 영역 하이라이트 + 스크롤 |
| 상태에 따른 힌트 | 빈 슬롯 있을 때/없을 때 다른 안내 텍스트 표시 |

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `guild-member` | 편성 대상 길드원 데이터 |
| `unlock-shop` | 프리셋 수·슬롯 수 확장 해금 |
| `command-post` | 길드원 관리 모달 내 편성 탭 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 아이디어 단계 초안 작성 (draft). |
| 2026-04-27 | HTML 구현 완료에 따라 승격. 전체 스펙 문서화. |
| 2026-04-29 | 편성 UX 개선: 벤치 카드에 + 오버레이·스탯 힌트 추가, 빈 슬롯 pulse 애니메이션, 상황별 안내 텍스트, 프리셋 멤버 수 표시, 스쿼드 full 시 벤치 비활성화. |

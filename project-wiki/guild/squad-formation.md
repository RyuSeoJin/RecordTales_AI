# Squad Formation — 스쿼드 편성

> **문서 성격**: `guild` 시스템의 **스쿼드 편성** 스펙.
> 보유 길드원을 최대 2~4명 슬롯에 배치하여 스쿼드를 편성하고, 프리셋으로 여러 조합을 관리하는 시스템.
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

- **한 줄 정의**: 보유 길드원을 최대 2~4명 슬롯에 배치하여 스쿼드를 편성하고, 프리셋으로 여러 조합을 관리하는 시스템.
- **위치**: Command Post → 길드원 관리 → 편성 탭
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
[스쿼드1 (2/2)]                               ← 프리셋 바 (초기 1개, 해금으로 확장)

Squad (2/2)
┌────┐ ┌────┐
│이미지│ │이미지│
│이름  │ │이름  │     ← 초기 2슬롯 (해금으로 3→4 확장)
│★★   │ │★★★  │
│  ✕  │ │  ✕  │
└────┘ └────┘

┌─────────┬─────────┐
│집중 시간  │골드 획득  │    ← 스탯 합산
│  +17%   │  +10%   │
└─────────┴─────────┘

"아래 대기 길드원을 클릭하면 빈 슬롯에 편성됩니다"

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

(v1과 동일 — 프리셋 바, 슬롯, 벤치 카드, 스탯 요약, 애니메이션 스펙 변경 없음)

## 3. 데이터 모델

### 3.1. 전역 상태 (`A`)

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.squadPresets` | `number[][]` | `[[...A.squad]]` | 프리셋별 길드원 ID 배열 (초기 1개, 기존 squad에서 마이그레이션) |
| `A.activePreset` | `number` | `0` | 현재 활성 프리셋 인덱스 |
| `A.squad` | `number[]` | `[]` | 활성 프리셋의 참조 (호환용) |

### 3.2. 상수

| 속성 | 타입 | 기본값 | 해금 후 | 설명 |
|------|------|--------|---------|------|
| `SQUAD_SLOTS` | `let number` | `2` | 3→4 | 현재 슬롯 수 (squad3/squad4 구매로 확장) |
| `PRESET_COUNT` | `let number` | `1` | 2→3 | 현재 프리셋 수 (preset2/preset3 구매로 확장) |

### 3.3. 해금 연동

| 해금 ID | 비용 | 효과 |
|---------|------|------|
| `preset2` | 8,000G | PRESET_COUNT 1→2, squadPresets 배열 확장 |
| `preset3` | 25,000G | PRESET_COUNT 2→3, squadPresets 배열 확장 |
| `squad3` | 15,000G | SQUAD_SLOTS 2→3 |
| `squad4` | 50,000G | SQUAD_SLOTS 3→4 |

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

### 4.5. 초기화

- 페이지 로드 시 `getActivePresetCount()` / `getActiveSquadSlots()`로 해금 상태 동기화
- `PRESET_COUNT`, `SQUAD_SLOTS`를 해금 ID 기반으로 설정

### 4.6. 함수 매핑

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
| `getActivePresetCount()` | unlockedIds 기반 프리셋 수 반환 |
| `getActiveSquadSlots()` | unlockedIds 기반 슬롯 수 반환 |

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
| `unlock-shop` | preset2/3, squad3/4 구매로 슬롯·프리셋 확장 |
| `command-post` | 길드원 관리 모달 내 편성 탭 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 아이디어 단계 초안 작성 (draft). |
| 2026-04-27 | HTML 구현 완료에 따라 승격. 전체 스펙 문서화. |
| 2026-04-29 | 편성 UX 개선: 벤치 카드에 + 오버레이·스탯 힌트 추가, 빈 슬롯 pulse 애니메이션, 상황별 안내 텍스트, 프리셋 멤버 수 표시, 스쿼드 full 시 벤치 비활성화. |
| 2026-05-06 | v2 스펙 반영. SQUAD_SLOTS 4→2, PRESET_COUNT 3→1, squadPresets [[]]로 변경, requires 체인 해금(preset2/3, squad3/4). |

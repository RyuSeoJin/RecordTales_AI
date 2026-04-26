# Quest Detail -- 퀘스트 상세 뷰

> **문서 성격**: `quests-panel`의 **상세 뷰** 시스템 스펙.
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

- **한 줄 정의**: 개별 퀘스트의 상세 정보를 표시하고 완료/중단/수정/삭제 액션을 제공하는 뷰
- **위치**: `detail-side-panel` > Quests 모드 > `questsView === 'detail'`
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
+-------------------------------------------+
| [← 뒤로]                                  |
|                                           |
| ┌─ detail-hd ───────────────────────────┐ |
| │ [☐/☑] 퀘스트 제목                     │ |
| └────────────────────────────────────────┘ |
|                                           |
| ┌─ detail-grid ─────────────────────────┐ |
| │ 상태       [ACTIVE]                    │ |
| │ 카테고리   공부                         │ |
| │ 반복       매주 금요일      (반복 시)   │ |
| │ 반복 기간  2026.04.25 ~ 2027.04.25     │ |
| │            (마스터 뷰 시)              │ |
| │ 발생일     2026.04.25 · 금요일         │ |
| │            (반복 인스턴스 뷰 시)       │ |
| │ 시작일     2026.04.25  (단발/마스터)   │ |
| │ 마감일     2026.05.10 [D-15]           │ |
| │            (단발성+마감일 시)           │ |
| │ ID         2026042500001               │ |
| │ 설명       (있을 경우)                  │ |
| └────────────────────────────────────────┘ |
|                                           |
| << 인스턴스 뷰일 때 >>                     |
| 중단 설정                       [toggle]   |
| ┌─ 중단 사유 (toggle ON 시) ────────────┐ |
| │ (textarea, onblur 자동 저장)           │ |
| └────────────────────────────────────────┘ |
|                                           |
| [반복 제거]  [삭제]  [닫기]  [수정]        |
|  (마스터+반복관리탭)                       |
+-------------------------------------------+
```

### 2.2. CSS 클래스 구조

```
.q-view.active
├── .btn.btn-flat             (← 뒤로)
├── .detail-hd (.detail-done .detail-suspended)
│   ├── .detail-chk (.done)   또는 .detail-chk-placeholder (마스터 뷰)
│   └── .detail-title
├── .detail-grid
│   ├── .dg-label + .dg-val   (상태)
│   │   └── .detail-status-badge (.overdue/.active/.upcoming/.done/.suspended)
│   ├── .dg-label + .dg-val   (카테고리)
│   ├── .dg-label + .dg-val   (반복 -- 반복 퀘스트만)
│   ├── .dg-label + .dg-val   (반복 기간 -- 마스터 뷰만)
│   ├── .dg-label + .dg-val   (발생일 -- 반복 인스턴스 뷰만)
│   ├── .dg-label + .dg-val   (시작일 -- 단발성 또는 마스터 뷰)
│   ├── .dg-label + .dg-val   (마감일 + D-day 뱃지 -- 단발성만)
│   ├── .dg-label + .dg-val   (ID -- DM Mono)
│   └── .dg-label + .dg-val   (설명 -- 있을 경우)
├── << 인스턴스 뷰 >>
│   ├── .field-row             (중단 설정 토글)
│   │   ├── .field-label
│   │   └── .toggle > input#qdSuspendToggle
│   └── .form-textarea#qdSuspendReason  (중단 시 사유 입력)
└── .btn-row
    ├── .btn.btn-flat.btn-danger  ([반복 제거] -- 마스터 뷰 + detailFromRecurTab만)
    ├── .btn.btn-flat.btn-danger  (삭제)
    ├── .btn.btn-flat             (닫기)
    └── .btn.btn-primary.todo     (수정)
```

### 2.3. 시각 요소 상세

**헤더 (`detail-hd`)**:

| 요소 | 설명 |
|------|------|
| 컨테이너 | flex, gap 11px, `--surface2` bg, border-radius 10px, padding 12px 14px |
| `.detail-chk` | 20x20px, border-radius 6px, border 1.5px `--text-muted` |
| `.detail-chk.done` | bg `--todo-c`, border `--todo-c`, `::after` content "✓" 12px bold `#1a1500` |
| `.detail-chk-placeholder` | 마스터 뷰 시 체크박스 대신 빈 공간 |
| `.detail-title` | 15px, 500, `--text-primary`, flex:1 |
| `.detail-done .detail-title` | line-through, `--text-muted` |

**상세 그리드 (`detail-grid`)**:

| 속성 | 값 |
|------|-----|
| 레이아웃 | grid, `auto 1fr`, gap 7px 14px |
| `.dg-label` | DM Mono 9px, uppercase, 0.1em spacing, `--text-muted` |
| `.dg-val` | 12px, `--text-secondary`, word-break:break-word, line-height 1.5 |

**상태 뱃지 (`detail-status-badge`)**:

| 상태 | 배경 | 테두리 | 텍스트 |
|------|------|--------|--------|
| `overdue` | `rgba(232,124,124,0.18)` | `rgba(232,124,124,0.35)` | `--danger` |
| `active` | `--todo-dim` | `rgba(232,200,124,0.3)` | `--todo-c` |
| `upcoming` | `--surface2` | `--border` | `--text-secondary` |
| `done` | `rgba(124,232,168,0.12)` | `rgba(124,232,168,0.25)` | `--success` |
| `suspended` | `--surface2` | `--border` (dashed) | `--text-muted` |

마스터 뷰에서는 상태 대신 "반복 마스터" 텍스트를 upcoming 스타일로 표시.

**발생일** (반복 인스턴스 뷰): `--todo-c` 색상, 500 weight, 뒤에 `· {요일}` (`--text-muted`, 400 weight)

**ID 표시**: DM Mono 10px, `--text-muted`, 0.05em spacing

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.questsView` | `string` | -- | `'detail'` |
| `A.detailTodoId` | `string\|null` | `null` | 상세 보기 대상 마스터 ID |
| `A.detailTodoDk` | `string\|null` | `null` | 인스턴스 날짜 키 (null = 마스터 뷰) |
| `A.detailFromRecurTab` | `boolean` | `false` | 반복 관리 탭에서 진입 여부 |

### 3.2. 데이터 스키마

Instance 스키마 및 MasterTodo 스키마는 `quests-panel.md` 3.2절 참조.

## 4. 동작 규칙

### 4.1. 상태 전이

없음. 인스턴스 상태 변경은 `quests-panel.md` 4.1절 참조.

### 4.2. 핵심 로직

#### 뷰 진입 경로 2가지

| 진입 경로 | detailTodoDk | detailFromRecurTab | 결과 |
|----------|-------------|-------------------|------|
| 리스트 아이템 클릭 | dk 값 | `false` | 인스턴스 뷰 |
| 반복 관리 탭 아이템 클릭 | `null` | `true` | 마스터 뷰 |

#### 마스터 뷰 vs 인스턴스 뷰 차이

| 항목 | 마스터 뷰 | 인스턴스 뷰 |
|------|----------|------------|
| 체크박스 | `.detail-chk-placeholder` (숨김) | `.detail-chk` (활성) |
| 상태 뱃지 | "반복 마스터" (upcoming 스타일) | 실제 상태 표시 |
| 반복 기간 | 표시 (`startDate ~ endDate`) | 미표시 |
| 발생일 | 미표시 | 표시 (`occurrenceKey · 요일`) |
| 시작일 | 표시 | 미표시 (반복 인스턴스) |
| 중단 설정 | 미표시 | 토글 표시 |
| [반복 제거] 버튼 | `detailFromRecurTab=true`일 때만 표시 | 미표시 |

#### 완료 토글 (detailToggleDone)

- 마스터 뷰(dk===null)에서는 동작 안 함
- `toggleInstanceDone(masterId, dk)` 호출 후 상세 뷰 + TL 오버레이 재렌더

#### 중단 토글 (detailToggleSuspend)

- 마스터 뷰에서는 동작 안 함 (UI에서 숨김)
- ON: `setInstanceSuspended(id, dk, true, '')` -> 사유 textarea 표시
- OFF: `setInstanceSuspended(id, dk, false, '')` -> 사유 초기화, 원래 상태 자동 복귀

#### 중단 사유 저장 (detailSetSuspendReason)

- `#qdSuspendReason` textarea의 `onblur` 이벤트로 자동 저장
- 단발성: `master.suspendReason` 갱신 (suspendedAt이 있을 때만)
- 반복: `master.recurData[dk].suspendReason` 갱신 (suspendedAt이 있을 때만)

#### 삭제 (detailDelete)

- confirm 다이얼로그: 반복이면 "모든 과거/미래 기록 포함" 경고
- `A.todos`에서 해당 마스터 제거
- 리스트 뷰로 복귀 + TL 오버레이 갱신

#### 반복 기간 표시 (마스터 뷰)

- 종료일: `recEndDate || startDate + 1년`
- 자동 종료일이면 뒤에 "(자동 · 1년)" 표시

#### 마감일 + D-day 뱃지 (단발성만)

- 단발성이고 `master.deadline`이 있을 때만 마감일 행 표시
- D-day 뱃지: `ddayInfoForKey(master.deadline)` 결과

### 4.3. 함수 매핑

| 함수 | 역할 |
|------|------|
| `openQuestDetail(id, dk)` | 인스턴스 뷰 진입 (리스트에서) |
| `openRecurringMasterDetail(masterId)` | 마스터 뷰 진입 (반복 관리 탭에서) |
| `renderQuestsDetail()` | 상세 뷰 HTML 생성 및 DOM 삽입 |
| `detailToggleDone()` | 인스턴스 완료 토글 |
| `detailToggleSuspend()` | 인스턴스 중단 토글 |
| `detailSetSuspendReason()` | 중단 사유 저장 (onblur) |
| `detailDelete()` | confirm 후 마스터 삭제 |
| `openQuestsForm(editId)` | 수정 버튼 -> 편집 폼 |
| `openRecurRemoveView(id)` | [반복 제거] -> 반복 제거 뷰 |
| `backToQuestsList()` | 뒤로/닫기 -> 리스트 복귀 |

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 대상 | 결과 |
|------|------|------|
| "← 뒤로" 클릭 | `.btn.btn-flat` | 리스트 뷰로 복귀 |
| 체크박스 클릭 | `.detail-chk` | 완료/미완료 토글 (인스턴스 뷰만) |
| 중단 토글 | `#qdSuspendToggle` | ON: 중단 + 사유 textarea 표시 / OFF: 중단 해제 |
| 중단 사유 입력 후 포커스 이탈 | `#qdSuspendReason` onblur | 사유 자동 저장 |
| "반복 제거" 클릭 | `.btn.btn-flat.btn-danger` | 반복 제거 뷰 진입 |
| "삭제" 클릭 | `.btn.btn-flat.btn-danger` | confirm -> 삭제 -> 리스트 복귀 |
| "닫기" 클릭 | `.btn.btn-flat` | 리스트 뷰로 복귀 |
| "수정" 클릭 | `.btn.btn-primary.todo` | 편집 폼 열기 |

## 6. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `quests-panel.md` | 퀘스트 시스템 전체 스펙, 인스턴스 상태 전이 |
| `quest-list.md` | 상세 뷰 진입/복귀 대상 리스트 |
| `quest-form.md` | "수정" 버튼 클릭 시 편집 폼 |
| `recurrence-removal.md` | "반복 제거" 버튼 클릭 시 진입 |
| `today-todo-overlay.md` | 완료/중단 토글 시 동기화 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 8.1 wiki 템플릿 기반 신규 작성 |

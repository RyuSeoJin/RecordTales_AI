# Recurrence Removal -- 반복 제거 뷰

> **문서 성격**: `quests-panel`의 **반복 제거 플로우** 시스템 스펙.
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

- **한 줄 정의**: 반복 퀘스트의 반복 주기를 제거(종료)하기 위한 캘린더 기반 날짜 선택 + 미리보기 뷰
- **위치**: `detail-side-panel` > Quests 모드 > `questsView === 'recurRemove'`
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
+-------------------------------------------+
| [← 뒤로]                                  |
|                                           |
| 반복 제거                   (빨간색 라벨)  |
|                                           |
| ┌─ rr-warn-banner ──────────────────────┐ |
| │ ⚠ 선택한 날짜부터 이 반복 퀘스트가    │ |
| │   더 이상 발생하지 않습니다.           │ |
| │   과거 완료 기록은 유지됩니다.         │ |
| └────────────────────────────────────────┘ |
|                                           |
| ┌─ rr-master-info ──────────────────────┐ |
| │ 매일 공부하기                          │ |
| │ 매일 · 2026.04.23 ~ 2027.04.23        │ |
| └────────────────────────────────────────┘ |
|                                           |
| 제거 시작일                                |
| ┌─ qcal-wrap (모든 날짜 선택 가능) ─────┐ |
| │ 2026  4월          [<] [>]            │ |
| │ 일 월 화 수 목 금 토                   │ |
| │  ... 미니 캘린더 ...                   │ |
| └────────────────────────────────────────┘ |
|                                           |
| ┌─ rr-preview (danger/warn/info) ───────┐ |
| │ ⚠/✓  미리보기 제목                    │ |
| │      미리보기 상세                     │ |
| └────────────────────────────────────────┘ |
|                                           |
|                   [취소]  [제거 적용]      |
+-------------------------------------------+
```

### 2.2. CSS 클래스 구조

```
.q-view.active
├── .btn.btn-flat                (← 뒤로)
├── .sec-label                   ("반복 제거", color:--danger)
├── .rr-warn-banner
│   ├── .rr-warn-banner-ico      (⚠)
│   └── div                      (경고 텍스트 + 서브텍스트)
├── .rr-master-info
│   ├── .rr-master-title         (마스터 제목)
│   └── .rr-master-meta          (규칙 · 기간)
├── .sec-label                   ("제거 시작일")
├── .qcal-wrap#qRecurRemoveCalWrap
│   ├── .qcal-hdr
│   │   ├── .qcal-title
│   │   └── .qcal-nav
│   ├── .qcal-wds
│   └── .qcal-grid
│       └── .qcal-day            (모든 날짜 활성 -- minKey:null)
├── .rr-preview (.danger / .warn / .info)
│   ├── .rr-preview-ico          (⚠ 또는 ✓)
│   └── .rr-preview-body
│       ├── .rr-preview-head     (미리보기 제목)
│       └── .rr-preview-sub      (상세 설명)
└── .btn-row
    ├── .btn.btn-flat            (취소)
    └── .btn.btn-primary.danger  (제거 적용, 미선택 시 disabled)
```

### 2.3. 시각 요소 상세

**경고 배너 (`rr-warn-banner`)**:

| 속성 | 값 |
|------|-----|
| 배경 | `rgba(232,124,124,0.08)` |
| 테두리 | `rgba(232,124,124,0.25)`, border-radius 8px |
| 텍스트 | 11.5px, `#f5b5b5`, line-height 1.55 |
| 아이콘 | `--danger`, 12px |
| 서브텍스트 | `--text-muted`, 10.5px |

**마스터 정보 카드 (`rr-master-info`)**:

| 속성 | 값 |
|------|-----|
| 배경 | `--surface2`, border-radius 8px, padding 10px 12px |
| `.rr-master-title` | 13px, 500, `--text-primary` |
| `.rr-master-meta` | DM Mono 10px, `--text-muted`, 0.03em spacing |

**미리보기 (`rr-preview`)**:

| 변형 | 테두리 | 배경 | 텍스트 |
|------|--------|------|--------|
| 기본 (미선택) | `--border` | `--surface2` | -- |
| `.danger` | `rgba(232,124,124,0.4)` | `rgba(232,124,124,0.08)` | `#f5b5b5` |
| `.warn` | `rgba(232,200,124,0.3)` | `--todo-dim` | `--todo-c` |
| `.info` | `rgba(124,232,168,0.25)` | `rgba(124,232,168,0.08)` | `--success` |

| 요소 | 설명 |
|------|------|
| `.rr-preview-ico` | 12px, flex-shrink:0, margin-top 1px |
| `.rr-preview-head` | font-weight 500, margin-bottom 2px |
| `.rr-preview-sub` | DM Mono 10px, 0.03em spacing, opacity 0.9 |

**캘린더**: `buildMiniCalHtml`과 동일하되 `minKey: null` -- 모든 날짜 선택 가능 (시작일 이전 포함).

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.questsView` | `string` | -- | `'recurRemove'` |
| `A.detailTodoId` | `string` | -- | 대상 마스터 ID |
| `A.recurRemoveCalYear` | `number` | 현재 연도 | 캘린더 연도 커서 |
| `A.recurRemoveCalMonth` | `number` | 현재 월(0-11) | 캘린더 월 커서 |
| `A.recurRemoveDate` | `string\|null` | `null` | 선택된 제거 시작일 |

### 3.2. 데이터 스키마

대상은 MasterTodo 레코드. 스키마는 `quests-panel.md` 3.2절 참조.

## 4. 동작 규칙

### 4.1. 상태 전이

없음. 날짜 선택 -> 미리보기 갱신 -> 적용/취소의 단순 플로우.

### 4.2. 핵심 로직

#### 뷰 진입 (openRecurRemoveView)

- `questsView = 'recurRemove'`, `detailTodoId = id`
- `recurRemoveDate = null`
- 캘린더 커서: 기존 `recEndDate` 위치, 없으면 `startDate > todayKey ? startDate : todayKey`

#### 캘린더 특성

- `minKey: null` -- **모든 날짜 선택 가능** (시작일 이전도 선택 가능 -> Case 1 전체 삭제 유도)
- 날짜 선택 시: `A.recurRemoveDate = dk` -> `renderRecurRemoveView()` 전체 재렌더

#### 미리보기 (buildRecurRemovePreview)

선택 날짜에 따라 3가지 케이스:

**Case 1: 시작일 이전 선택 (`picked < master.startDate`)**
- 스타일: `.danger` (빨간색)
- 아이콘: ⚠
- 제목: "반복 퀘스트 전체가 삭제됩니다"
- 설명: "선택일이 시작일({startDate}) 이전입니다. 모든 과거 기록도 사라집니다."

**Case 2: 오늘 또는 과거 선택 (`picked <= todayKey`, 시작일 이후)**
- 스타일: `.warn` (노란색)
- 아이콘: ⚠
- 제목: "{picked}부터 반복 중단"
- 설명: "종료일이 {picked-1일}로 변경됩니다. {picked} 이후의 미완료 인스턴스가 사라집니다."

**Case 3: 미래 선택 (`picked > todayKey`)**
- 스타일: `.info` (초록색)
- 아이콘: ✓
- 제목: "{picked}부터 반복 중단"
- 설명: "종료일이 {picked-1일}로 변경됩니다."

**미선택 상태**:
- 기본 스타일
- 아이콘: 📅
- 제목: "제거 시작일을 선택해주세요"
- 설명: "선택한 날짜부터 반복이 중단됩니다."

#### 적용 (applyRecurRemove)

| 케이스 | 처리 | confirm 필요 |
|--------|------|-------------|
| Case 1 | `A.todos`에서 마스터 제거 (전체 삭제) | 예 -- "아예 제거하시겠습니까?" |
| Case 2 | `master.recEndDate = addDays(picked, -1)` | 예 -- "제거하시겠습니까?" |
| Case 3 | `master.recEndDate = addDays(picked, -1)` | 아니오 -- 바로 적용 |

적용 후:
- 토스트: Case 1 "반복 퀘스트가 삭제되었습니다" / Case 2-3 "반복이 제거되었습니다"
- `questsView = 'list'`, `questsTab = 'recurring'` (반복 관리 탭으로 복귀)
- 모든 관련 상태 초기화
- `renderQuestsList()` + `renderTlTodos()` + archive 갱신

#### 취소 (cancelRecurRemove)

- `recurRemoveDate = null`
- 마스터 상세 뷰로 복귀: `questsView = 'detail'`, `detailTodoDk = null`, `detailFromRecurTab = true`
- `renderQuestsDetail()` 호출

### 4.3. 함수 매핑

| 함수 | 역할 |
|------|------|
| `openRecurRemoveView(id)` | 반복 제거 뷰 진입, 캘린더 초기화 |
| `renderRecurRemoveView()` | 전체 뷰 HTML 생성 및 DOM 삽입 |
| `renderRecurRemoveCalHtml()` | 캘린더 HTML (minKey:null) |
| `moveRecurRemoveCal(delta)` | 캘린더 월 이동 |
| `selectRecurRemoveDate(dk)` | 날짜 선택 -> 전체 재렌더 |
| `buildRecurRemovePreview(m)` | 선택 날짜 기반 미리보기 HTML 생성 |
| `applyRecurRemove()` | 케이스별 처리 적용 |
| `cancelRecurRemove()` | 취소 -> 마스터 상세 복귀 |

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 대상 | 결과 |
|------|------|------|
| "← 뒤로" 클릭 | `.btn.btn-flat` | 마스터 상세 뷰로 복귀 (cancelRecurRemove) |
| 캘린더 날짜 클릭 | `.qcal-day` | 제거 시작일 선택, 미리보기 갱신 |
| 캘린더 ◂/▸ | `.qcal-nav button` | 이전/다음 월 이동 |
| "취소" 클릭 | `.btn.btn-flat` | 마스터 상세 뷰로 복귀 |
| "제거 적용" 클릭 | `.btn.btn-primary.danger` | 케이스별 처리 (미선택 시 disabled) |

## 6. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `quests-panel.md` | 퀘스트 시스템 전체 스펙, 인스턴스/마스터 모델 |
| `quest-detail.md` | [반복 제거] 버튼으로 진입하는 상세 뷰 |
| `quest-list.md` | 적용 후 복귀 대상인 리스트 뷰 (반복 관리 탭) |
| `today-todo-overlay.md` | 적용 후 동기화 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 8.1 wiki 템플릿 기반 신규 작성 |

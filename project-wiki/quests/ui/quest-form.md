# Quest Form -- 퀘스트 생성/편집 폼

> **문서 성격**: `quests-panel`의 **생성/편집 폼** 시스템 스펙.
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

- **한 줄 정의**: 퀘스트 생성(create) 및 편집(edit) 폼 -- 제목, 설명, 카테고리, 반복 주기, 시작일, 마감일, 반복 종료일을 입력
- **위치**: `detail-side-panel` > Quests 모드 > `questsView === 'create' | 'edit'`
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
+-------------------------------------------+
| [← 뒤로]                                  |
| 새 퀘스트 / 퀘스트 수정                    |
|                                           |
| ┌─ 제목 * ──────────────────────────────┐ |
| │                                        │ |
| └────────────────────────────────────────┘ |
| ┌─ 설명 (선택) ─────────────────────────┐ |
| │                                        │ |
| │                                        │ |
| └────────────────────────────────────────┘ |
|                                           |
| 카테고리 *                                 |
| [공부] [프로젝트] [업무] [독서] [운동]     |
| [+ 카테고리 추가]                          |
|                                           |
| 반복 주기                                  |
| [없음] [매일] [매주] [매달]                |
|                                           |
| << 반복 선택 시 추가 표시 >>               |
| 시작일 *              2026.04.25           |
| ┌─ qcal-wrap ───────────────────────────┐ |
| │ 2026  4월          [<] [>]            │ |
| │ 일 월 화 수 목 금 토                   │ |
| │  ... 미니 캘린더 ...                   │ |
| └────────────────────────────────────────┘ |
|                                           |
| 반복 종료일 설정                [toggle]   |
| (미설정 시 시작일+1년 자동)                 |
| ┌─ 종료일 캘린더 (토글 ON 시) ──────────┐ |
| └────────────────────────────────────────┘ |
|                                           |
| 🔁 2026.04.25부터 2027.04.25까지 매일     |
|                                           |
| 마감일 설정                     [toggle]   |
| << 반복: 오프셋 입력 >>                    |
| ┌─────────────────────────────────────┐   |
| │ 발생일로부터  [  3  ]  일 후         │   |
| └─────────────────────────────────────┘   |
| << 단발성: 캘린더 >>                       |
| ┌─ 마감일 캘린더 ───────────────────────┐ |
| └────────────────────────────────────────┘ |
|                                           |
|                     [취소]  [저장/수정완료] |
+-------------------------------------------+
```

### 2.2. CSS 클래스 구조

```
.q-view.active
├── .btn.btn-flat          (← 뒤로 버튼)
├── .sec-label             (섹션 라벨: "새 퀘스트"/"퀘스트 수정")
├── .form-input#qTitle     (제목 입력)
├── .form-textarea#qDesc   (설명 입력)
├── .sec-label             ("카테고리 *")
├── .cat-chips-wrap#qCatChips
│   └── .cat-chip (.sel)
│       ├── span           (색상 dot, 있을 경우)
│       ├── text           (카테고리명)
│       └── .cat-chip-del  (x 삭제 버튼)
├── .cat-add-open-btn      ("+ 카테고리 추가" -> 모달)
├── .sec-label             ("반복 주기")
├── .rec-row#qRecRow
│   ├── .rec-chip (.sel)   (없음)
│   ├── .rec-chip (.sel)   (매일)
│   ├── .rec-chip (.sel)   (매주)
│   └── .rec-chip (.sel)   (매달)
├── << 반복 시 >>
│   ├── .form-section-label
│   │   ├── .sec-label     ("시작일 *")
│   │   └── .sec-val       (선택된 날짜)
│   ├── .qcal-wrap#qStartCalWrap
│   │   ├── .qcal-hdr
│   │   │   ├── .qcal-title  (연도 + 월)
│   │   │   └── .qcal-nav    (◂ ▸ 버튼)
│   │   ├── .qcal-wds        (요일 헤더)
│   │   └── .qcal-grid       (날짜 그리드)
│   │       └── .qcal-day (.empty .past .sun .sat .today .sel)
│   ├── .field-row           (반복 종료일 토글)
│   │   ├── .field-label
│   │   └── .toggle > input#qHasRecEnd
│   ├── .form-toggle-hint    (종료일 미설정 안내)
│   ├── .qcal-wrap#qRecEndCalWrap  (토글 ON 시)
│   └── .rec-summary         (반복 규칙 요약)
│       ├── .rec-summary-icon (🔁)
│       └── span              (요약 텍스트)
├── .field-row               (마감일 토글)
│   ├── .field-label
│   └── .toggle > input#qHasDeadline
├── #qDeadlineCalWrap
│   ├── << 반복: >> .deadline-offset-wrap
│   │   ├── .deadline-offset-label  ("발생일로부터")
│   │   ├── .deadline-offset-input#qDeadlineOffset
│   │   └── .deadline-offset-label  ("일 후")
│   └── << 단발성: >> .qcal-wrap  (마감일 캘린더)
└── .btn-row
    ├── .btn.btn-flat        (취소)
    └── .btn.btn-primary.todo (저장/수정 완료)
```

### 2.3. 시각 요소 상세

**입력 필드**:

| 요소 | 폰트 | 크기 | 기타 |
|------|------|------|------|
| `.form-input` | Noto Sans KR | 13px | `--surface2` bg, border-radius 8px, padding 9px 12px, focus: border `rgba(232,200,124,0.35)` |
| `.form-textarea` | Noto Sans KR | 13px | min-height 70px, resize:none, line-height 1.6 |
| placeholder | -- | -- | `--text-muted` 색상 |

**카테고리 칩 (`cat-chip`)**:

| 속성 | 값 |
|------|-----|
| 기본 | `--surface2` bg, border-radius 14px, Noto Sans KR 11px |
| `.sel` | `--focus-dim` bg, `rgba(232,168,124,0.4)` border, `--focus-c` 색상 |
| 커스텀 색상 적용 시 `.sel` | `{color}22` bg, `{color}` border/text |
| `.cat-chip-del` | 13px, `--text-muted` -> hover: `--danger` |

**반복 주기 칩 (`rec-chip`)**:

| 속성 | 값 |
|------|-----|
| 레이아웃 | flex:1, min-width 56px, text-align center |
| 기본 | `--surface2` bg, border-radius 8px, Noto Sans KR 11px, `--text-secondary` |
| `.sel` | `--todo-dim` bg, `rgba(232,200,124,0.4)` border, `--todo-c` 색상 |

**미니 캘린더 (`qcal-wrap`)**:

| 요소 | 설명 |
|------|------|
| `.qcal-wrap` | `--surface` bg, border-radius 10px, padding 12px |
| `.qcal-title` | DM Serif Display 14px, 월 표시는 DM Mono 11px `--text-muted` |
| `.qcal-nav button` | 22x22px, border-radius 5px |
| `.qcal-wd` | DM Mono 7px, uppercase, 일요일 빨강, 토요일 파랑 |
| `.qcal-day` | aspect-ratio 1, DM Mono 10px, border-radius 5px |
| `.qcal-day.sel` | `--todo-c` bg, `#1a1500` 텍스트, font-weight 600 |
| `.qcal-day.today` | border 1px `rgba(232,200,124,0.3)` |
| `.qcal-day.past` | pointer-events:none, opacity 0.4 |
| `.qcal-day.sun` | `rgba(232,124,124,0.7)` |
| `.qcal-day.sat` | `rgba(124,184,232,0.7)` |

**마감일 오프셋 (`deadline-offset-wrap`)**:

| 속성 | 값 |
|------|-----|
| 컨테이너 | flex, gap 8px, `--surface2` bg, border-radius 8px, padding 10px 12px |
| `.deadline-offset-label` | Noto Sans KR 12px, `--text-secondary` |
| `.deadline-offset-input` | 60px 너비, DM Mono 14px 500, text-align center, 스핀 버튼 표시 |

**반복 규칙 요약 (`rec-summary`)**:

| 속성 | 값 |
|------|-----|
| 배경 | `--surface2`, border `rgba(232,200,124,0.18)`, border-radius 8px |
| 폰트 | 11.5px, line-height 1.5 |
| 날짜 강조 | `--todo-c`, DM Mono 11px, 500 |
| 아이콘 | 🔁, `--todo-c` 11px |

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.questsView` | `string` | -- | `'create'` 또는 `'edit'` |
| `A.editingTodoId` | `string\|null` | `null` | 편집 시 대상 마스터 ID |
| `A.todoSelCat` | `string\|null` | `null` | 선택된 카테고리 |
| `A.todoSelRec` | `string` | `'none'` | 선택된 반복 주기 |
| `A.todoStartDate` | `string\|null` | -- | 시작일 (생성 시 todayKey 기본) |
| `A.todoHasDeadline` | `boolean` | `false` | 마감일 토글 |
| `A.todoDeadline` | `string\|null` | `null` | 마감일 (단발성용) |
| `A.todoDeadlineOffset` | `number` | `1` | 마감일 오프셋 (반복용, 1~365) |
| `A.todoHasRecEnd` | `boolean` | `false` | 반복 종료일 토글 |
| `A.todoRecEnd` | `string\|null` | `null` | 반복 종료일 |
| `A.questStartCalYear/Month` | `number` | 현재 | 시작일 캘린더 커서 |
| `A.questDeadlineCalYear/Month` | `number` | 현재 | 마감일 캘린더 커서 |
| `A.questRecEndCalYear/Month` | `number` | 현재 | 반복 종료일 캘린더 커서 |
| `A.categories` | `string[]` | 기본 6개 | 카테고리 목록 |
| `A.catColors` | `Object` | `{}` | 카테고리별 색상 |

### 3.2. 데이터 스키마

저장 시 생성/갱신되는 MasterTodo 스키마는 `quests-panel.md` 3.2절 참조.

## 4. 동작 규칙

### 4.1. 상태 전이

없음. 폼은 단순 입력 -> 저장/취소 플로우.

### 4.2. 핵심 로직

#### 폼 열기 (openQuestsForm)

**생성 모드** (`editId` 없음):
- `questsView = 'create'`, `editingTodoId = null`
- 카테고리/반복 초기화: `todoSelCat = null`, `todoSelRec = 'none'`
- 시작일: `todayKey()`
- 마감일/종료일: 모두 미설정

**편집 모드** (`editId` 있음):
- `questsView = 'edit'`, `editingTodoId = editId`
- 기존 마스터에서 모든 필드 프리필
- 반복 퀘스트: `deadlineOffset` 값 복원, `deadline = null`
- 단발성 퀘스트: `deadline` 값 복원 (단, deadline === startDate이면 "미설정"으로 간주)

#### 반복 주기 변경 (setQRec)

- `todoSelRec` 업데이트
- 'none' -> recurring 전환 시: `todoStartDate`가 없으면 `todayKey()` 세팅
- `rerenderForm()` 호출 (타이핑 값 보존)

#### 캘린더 (3종 공유: buildMiniCalHtml)

공통 파라미터:

| 파라미터 | 설명 |
|---------|------|
| `year`, `month` | 캘린더 커서 위치 |
| `selected` | 현재 선택된 날짜 (`.sel` 표시) |
| `minKey` | 이 날짜 이전은 `.past` (비활성) |
| `navFn` | 월 이동 콜백 이름 |
| `dayFn` | 날짜 선택 콜백 이름 |

**시작일 캘린더**: minKey = todayKey() (과거 비활성)
**반복 종료일 캘린더**: minKey = todoStartDate (시작일 이전 비활성)
**마감일 캘린더**: minKey = todoStartDate (시작일 이전 비활성)

#### 시작일 선택 시 연쇄 리셋

`selectStartDate(dk)` 호출 시:
- 반복 종료일이 시작일보다 이전이면: `todoRecEnd = null`, `todoHasRecEnd = false`
- 마감일이 시작일보다 이전이면: `todoDeadline = null`, `todoHasDeadline = false`

#### 반복 규칙 요약 (buildRecSummaryHtml)

- 반복 미설정 또는 시작일 미선택: "시작일을 선택하면 반복 규칙이 요약됩니다."
- daily: "`{시작일}`부터 `{종료일}`까지 매일"
- weekly: "`{시작일}`부터 `{종료일}`까지 매주 {요일}요일"
- monthly: "`{시작일}`부터 `{종료일}`까지 매달 {일}일"
- 종료일 미설정 시: startDate + 1년, 뒤에 "(자동)" 표시

#### 저장 검증 (saveQuest)

| 조건 | 에러 메시지 |
|------|------------|
| 제목 비어있음 | "제목을 입력해주세요" |
| 카테고리 미선택 | "카테고리를 선택해주세요" |
| 반복인데 시작일 없음 | "시작일을 선택해주세요" |
| 반복 + 종료일 토글 ON인데 미선택 | "종료일을 선택해주세요" |
| 종료일 < 시작일 | "종료일은 시작일 이후여야 합니다" |
| 반복 + 마감일 토글 ON인데 오프셋 < 1 | "마감일 일수를 1 이상으로 입력해주세요" |
| 단발성 + 마감일 토글 ON인데 미선택 | "마감일을 선택해주세요" |
| 단발성 + 마감일 < 시작일 | "마감일은 시작일 이후여야 합니다" |

#### 저장 처리

**생성**:
- ID: `nextIdForDate(dkToCompact(startDate))` -- 13자리
- 기본값: `doneAt:null`, `recurData:{}`, `isFavorite:false`
- `A.todos.push(newTodo)`
- 토스트: "추가되었습니다"

**편집**:
- 기존 객체의 필드만 업데이트 (ID, doneAt, recurData, isFavorite 유지)
- 토스트: "수정되었습니다"

저장 후: `resetQuestFormState()` -> `questsView = 'list'` -> `renderQuestsList()` + `renderTlTodos()`

#### 폼 상태 초기화 (resetQuestFormState)

```
editingTodoId = null
todoSelCat = null, todoSelRec = 'none'
todoStartDate = null
todoHasRecEnd = false, todoRecEnd = null
todoHasDeadline = false, todoDeadline = null, todoDeadlineOffset = 1
```

### 4.3. 함수 매핑

| 함수 | 역할 |
|------|------|
| `openQuestsForm(editId?)` | 폼 상태 초기화 + 편집 시 프리필 + 렌더링 |
| `renderQuestsForm(t?)` | 폼 HTML 생성 및 DOM 삽입 |
| `rerenderForm()` | 제목/설명 입력값 보존하며 재렌더 |
| `setQRec(r)` | 반복 주기 변경 |
| `toggleDeadline()` | 마감일 토글 -> rerenderForm |
| `toggleRecEnd()` | 반복 종료일 토글 -> rerenderForm |
| `setDeadlineOffset(v)` | 마감일 오프셋 값 변경 (1~365 clamping) |
| `renderStartCalHtml()` | 시작일 캘린더 HTML |
| `moveStartCal(delta)` | 시작일 캘린더 월 이동 |
| `selectStartDate(dk)` | 시작일 선택 + 연쇄 리셋 |
| `renderRecEndCalHtml()` | 반복 종료일 캘린더 HTML |
| `moveRecEndCal(delta)` | 종료일 캘린더 월 이동 |
| `selectRecEndDate(dk)` | 종료일 선택 |
| `renderDeadlineCalHtml()` | 마감일 캘린더 HTML |
| `moveDeadlineCal(delta)` | 마감일 캘린더 월 이동 |
| `selectDeadline(dk)` | 마감일 선택 |
| `buildRecSummaryHtml()` | 반복 규칙 요약 텍스트 생성 |
| `renderQCatChips()` | 카테고리 칩 렌더링 |
| `selectQCat(cat)` | 카테고리 선택/해제 토글 |
| `delQCat(i)` | 카테고리 삭제 |
| `saveQuest()` | 검증 + 저장 |
| `resetQuestFormState()` | 폼 상태 초기화 |
| `backToQuestsList()` | 리스트로 복귀 |
| `buildMiniCalHtml(opts)` | 미니 캘린더 공통 빌더 |

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 대상 | 결과 |
|------|------|------|
| "← 뒤로" 클릭 | `.btn.btn-flat` | 폼 상태 초기화 후 리스트로 복귀 |
| 제목 입력 | `#qTitle` | 최대 80자, placeholder "제목 *" |
| 설명 입력 | `#qDesc` | 자유 텍스트, placeholder "설명 (선택)" |
| 카테고리 칩 클릭 | `.cat-chip` | 선택/해제 토글 (1개만 선택 가능) |
| 카테고리 x 클릭 | `.cat-chip-del` | 해당 카테고리 삭제 |
| "+ 카테고리 추가" 클릭 | `.cat-add-open-btn` | 카테고리 추가 모달 열기 |
| 반복 주기 칩 클릭 | `.rec-chip` | 반복 주기 변경, 폼 재렌더 |
| 캘린더 날짜 클릭 | `.qcal-day` | 해당 날짜 선택 |
| 캘린더 ◂/▸ 클릭 | `.qcal-nav button` | 이전/다음 월 이동 |
| 반복 종료일 토글 | `#qHasRecEnd` | ON: 캘린더 표시 / OFF: 종료일 null |
| 마감일 토글 | `#qHasDeadline` | ON: 오프셋 또는 캘린더 / OFF: 마감일 null |
| 오프셋 숫자 입력 | `#qDeadlineOffset` | 1~365 범위 clamping |
| "취소" 클릭 | `.btn.btn-flat` | 리스트로 복귀 |
| "저장"/"수정 완료" 클릭 | `.btn.btn-primary.todo` | 검증 -> 저장 -> 리스트로 복귀 |

## 6. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `quests-panel.md` | 퀘스트 시스템 전체 스펙, MasterTodo 스키마 |
| `quest-list.md` | 폼 진입/복귀 대상인 리스트 뷰 |
| `quest-detail.md` | 상세에서 "수정" 클릭 시 편집 폼 진입 |
| `today-todo-overlay.md` | 저장 시 동기화되는 오버레이 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 8.1 wiki 템플릿 기반 신규 작성 |

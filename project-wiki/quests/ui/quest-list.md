# Quest List -- 퀘스트 리스트 뷰

> **문서 성격**: `quests-panel`의 **리스트 뷰** 시스템 스펙.
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

- **한 줄 정의**: 퀘스트 목록을 탭(즐겨찾기/반복 관리)과 상태 필터로 분류하여 표시하는 메인 리스트 뷰
- **위치**: `detail-side-panel` > Quests 모드 > `questsView === 'list'`
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
+-------------------------------------------+
| [★ 즐겨찾기] [🔁 반복 관리]              |
|-------------------------------------------|
| << 즐겨찾기 탭일 때 >>                     |
| ┌ fav-row (수평 스크롤) ────────────────┐ |
| │ ★Quest1  ★Quest2  ★Quest3  ...       │ |
| └───────────────────────────────────────┘ |
|                                           |
| << 반복 관리 탭일 때 >>                    |
| ┌ rec-manage-list (max 180px) ──────────┐ |
| │ ┌─────────────────────────────────┐   │ |
| │ │ 매일 공부하기     [반복·매일]    │   │ |
| │ │ 2026.04.23 ~ 2027.04.23 · 자동 │   │ |
| │ └─────────────────────────────────┘   │ |
| │ ┌─────────────────────────────────┐   │ |
| │ │ 주간 리뷰        [반복·매주 금] │   │ |
| │ │ 2026.04.25 ~ 2026.12.31        │   │ |
| │ └─────────────────────────────────┘   │ |
| └───────────────────────────────────────┘ |
| ─── divider ───                           |
| [+ 새 퀘스트 추가]                        |
| ┌ filter-chips ─────────────────────────┐ |
| │ [전체] [지연 2] [진행예정] [진행중]    │ |
| │ [완료] [중단]                          │ |
| └───────────────────────────────────────┘ |
|                                           |
| ── 지연 · 2 ──────────────────────────── |
| ☐ 과제 제출      [공부] [D+3]    ★ 🗑   |
|   미완료 과제 정리                         |
| ☐ 보고서 마감    [업무] [D+1]    ★ 🗑   |
|                                           |
| ── 진행 중 · 1 ───────────────────────── |
| ☐ 알고리즘 풀기  [공부] [반복·매일] ★ 🗑 |
|                                           |
| ── 진행 예정 · 1 ──────────────────────── |
| ☐ 독서 모임      [독서]          ★ 🗑   |
|                                           |
| [더보기 (3건)]                            |
+-------------------------------------------+
```

### 2.2. CSS 클래스 구조

```
.q-view.active
├── .qtab-bar
│   └── .qtab (.on)
│       ├── .qtab-ico          (탭 아이콘: ★ / 🔁)
│       └── span               (탭 라벨)
├── .qtab-content
│   ├── << 즐겨찾기 탭 >>
│   │   └── .fav-row           (수평 스크롤, scrollbar 숨김)
│   │       ├── .fav-chip      (즐겨찾기 칩)
│   │       │   ├── .fav-star  (★ 아이콘)
│   │       │   └── span       (퀘스트 제목)
│   │       └── .fav-empty     (빈 상태: "★ 아이콘으로 즐겨찾기 추가")
│   └── << 반복 관리 탭 >>
│       └── .rec-manage-list   (max-height:180px, overflow-y:auto)
│           └── .rec-manage-item
│               ├── .rec-manage-top
│               │   ├── .rec-manage-title
│               │   └── .tbadge.rec   (규칙 요약)
│               └── .rec-manage-sub   (기간 표시)
├── .divider-line
├── .todo-add-btn              (+ 새 퀘스트 추가)
├── .filter-chips
│   └── .fchip (.on)
│       └── .fchip-badge       (지연 카운트, 빨간 원)
├── .todo-list
│   ├── .qlist-section-hdr     (.overdue/.active/.upcoming/.done/.suspended)
│   └── .todo-item (.done .overdue .suspended)
│       ├── .todo-chk (.done)
│       │   └── svg            (체크마크)
│       ├── .todo-content
│       │   ├── .todo-title-text
│       │   ├── .todo-badges
│       │   │   ├── .tbadge           (카테고리)
│       │   │   ├── .tbadge.rec       (반복 규칙)
│       │   │   └── .tbadge.dday      (.near/.today/.overdue)
│       │   └── .todo-desc-text       (설명)
│       └── .todo-acts
│           ├── .todo-act-btn.star (.on)
│           └── .todo-act-btn.btn-danger
├── .load-more-btn
└── .todo-empty-wrap
    ├── .todo-empty-icon       (📜)
    └── .todo-empty-msg
```

### 2.3. 시각 요소 상세

**탭 바 (`qtab-bar`)**:

| 속성 | 값 |
|------|-----|
| 배경 | `--surface2`, border `--border`, border-radius 9px |
| 패딩 | 3px (내부 간격) |
| `.qtab` 기본 | transparent bg, `--text-muted`, Noto Sans KR 11.5px |
| `.qtab.on` | `--todo-dim` bg, `--todo-c` 색상 |

**즐겨찾기 칩 (`fav-chip`)**:

| 속성 | 값 |
|------|-----|
| 레이아웃 | flex, align-items:center, gap:5px, flex-shrink:0, white-space:nowrap |
| 배경 | `--surface2`, border-radius 16px |
| 폰트 | Noto Sans KR 11px, `--text-secondary` |
| hover | `--todo-dim` bg, `--todo-c` 색상 |
| `.fav-star` | `--todo-c`, 10px |

**반복 관리 리스트 (`rec-manage-list`)**:

| 속성 | 값 |
|------|-----|
| max-height | 180px, overflow-y:auto |
| scrollbar | thin, `--border` 색상, 5px 너비 |
| `.rec-manage-item` | `--surface2` bg, border-radius 8px, padding 9px 11px |
| `.rec-manage-title` | 12.5px, 500, text-overflow:ellipsis |
| `.rec-manage-sub` | DM Mono 9.5px, `--text-muted` |

**상태 필터 칩 (`fchip`)**:

| 상태 | 활성 배경 | 활성 border | 활성 텍스트 |
|------|----------|------------|------------|
| 전체 | `#c8c8d822` | `#c8c8d8` | `#c8c8d8` |
| 지연 | `#e87c7c22` | `#e87c7c` | `#e87c7c` |
| 진행 예정 | `#c79de822` | `#c79de8` | `#c79de8` |
| 진행 중 | `#7cb8e822` | `#7cb8e8` | `#7cb8e8` |
| 완료 | `#7ce8a822` | `#7ce8a8` | `#7ce8a8` |
| 중단 | `#a0a0a022` | `#a0a0a0` | `#a0a0a0` |

- 지연 칩은 카운트 > 0이면 비활성 상태에서도 `border-color:rgba(232,124,124,0.4); color:#e87c7c99` 적용
- `.fchip-badge`: min-width 14px, height 14px, `--danger` bg, `#1a0a0a` 글자, 8px bold

**퀘스트 리스트 아이템 (`todo-item`)**:

| 속성 | 값 |
|------|-----|
| 레이아웃 | flex, align-items:flex-start, gap:10px |
| 배경 | `--surface2`, border-radius 11px, padding 11px 13px |
| `.todo-item.done` | opacity:0.5 |
| `.todo-item.overdue` | border-color:`rgba(232,124,124,0.35)`, bg:`rgba(232,124,124,0.05)` |
| `.todo-chk` | 18x18px, border-radius 5px, border 1.5px `--text-muted` |
| `.todo-chk.done` | bg `--todo-c`, border `--todo-c`, svg opacity:1 |
| `.todo-chk svg` | 10x10px, `#1a1500`, 기본 opacity:0 |

**섹션 헤더 (`qlist-section-hdr`)**:

| 상태 | 색상 |
|------|------|
| `.overdue` | `--danger` |
| `.active` | `--todo-c` |
| `.upcoming` | `--text-secondary` |
| `.done` | `--success` |
| `.suspended` | `--text-muted` |

형식: `{상태명} · {건수}` (DM Mono 8.5px, 0.22em spacing, uppercase)

**빈 상태 (`todo-empty-wrap`)**:
- 아이콘: 📜 (28px, opacity 0.3)
- 메시지: "{상태명} 상태의 퀘스트가 없습니다." (12px, `--text-muted`)

## 3. 데이터 모델

### 3.1. 전역 상태

| 속성 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.questsTab` | `'favorites'\|'recurring'` | `'favorites'` | 현재 선택된 탭 |
| `A.statusFilter` | `string` | `'all'` | 현재 상태 필터 |
| `A.visibleCount` | `number` | `10` | 표시 중인 아이템 수 |
| `A.todos` | `Array` | `[]` | 마스터 퀘스트 목록 (데이터 소스) |

### 3.2. 데이터 스키마

리스트에 표시되는 각 행은 `Instance` 객체 (`getInstance()` 반환값). 상세 스키마는 `quests-panel.md` 3.2절 참조.

## 4. 동작 규칙

### 4.1. 상태 전이

뷰 자체의 상태 전이는 없음. 인스턴스 상태 전이는 `quests-panel.md` 4.1절 참조.

### 4.2. 핵심 로직

#### 탭 컨텐츠 렌더링

- **즐겨찾기 탭**: `A.todos.filter(t => t.isFavorite)` -> ID asc 정렬 -> 각각 `.fav-chip`으로 렌더
  - 칩 클릭(`dupFav`) -> 오늘 단발성 퀘스트로 복제 (recurrence:'none', deadline:todayKey, isFavorite:false)
  - 비어 있으면: "★ 아이콘으로 즐겨찾기 추가" 텍스트
- **반복 관리 탭**: `A.todos.filter(t => t.recurrence !== 'none')` -> startDate asc, ID asc 정렬
  - 규칙 요약: daily->"매일", weekly->"매주 {요일}", monthly->"매달 {일}일"
  - 기간: `startDate ~ (recEndDate || startDate+1년) · 자동`
  - 아이템 클릭 -> `openRecurringMasterDetail(id)` -> 마스터 상세 (detailFromRecurTab=true)

#### 리스트 렌더링

**statusFilter === 'all'**:
1. 섹션 순서: 지연 -> 진행 중 -> 진행 예정 -> 완료 -> 중단
2. 각 섹션별 `filterInstancesByStatus()` + `sortInstancesForStatus()` 수행
3. 전체 합산하여 `visibleCount`만큼 잘라냄 (섹션 순서대로 채움)
4. 섹션 헤더: `{상태명} · {해당 상태 전체 건수}`

**statusFilter !== 'all'**:
1. 해당 상태만 필터 + 정렬
2. `visibleCount`만큼 슬라이스
3. 0건이면 빈 상태 메시지

**페이지네이션**:
- 전체 건수 > visibleCount이면 "더보기 ({남은}건)" 버튼 표시
- 클릭 시 `visibleCount += 10`

#### D-day 뱃지 표시 규칙

- 반복의 upcoming 인스턴스: D-day 미표시
- 단발성인데 별도 마감일 없음(deadline === null): D-day 미표시
- 그 외: `ddayInfoForKey(inst.deadline || inst.startDate)` 결과 표시

#### 반복 태그 표시

- `recurrence !== 'none'`이면 `[반복 · {규칙}]` 뱃지 표시
  - daily: "반복 · 매일"
  - weekly: "반복 · 매주 {요일}" (요일 뒤 '요일' 제거)
  - monthly: "반복 · 매달 {일}일"

### 4.3. 함수 매핑

| 함수 | 역할 |
|------|------|
| `renderQuestsList()` | 전체 리스트 뷰 HTML 생성 및 DOM 삽입 |
| `renderFavoritesTab()` | 즐겨찾기 탭 HTML |
| `renderRecurringManageTab()` | 반복 관리 탭 HTML |
| `renderQuestListItem(inst)` | 개별 아이템 HTML |
| `renderQuestsEmpty(label)` | 빈 상태 HTML |
| `setQuestsTab(tab)` | 탭 전환 -> renderQuestsList |
| `setStatusFilter(f)` | 필터 변경, visibleCount=10 -> renderQuestsList |
| `loadMoreQuests()` | visibleCount += 10 -> renderQuestsList |
| `qToggleDone(masterId, dk)` | 체크박스 토글 -> renderQuestsList + renderTlTodos |
| `qToggleFav(masterId)` | 즐겨찾기 토글 -> renderQuestsList |
| `qDeleteTodo(masterId)` | confirm 삭제 -> renderQuestsList + renderTlTodos |
| `dupFav(id)` | 즐겨찾기 복제 -> renderQuestsPanel + renderTlTodos |
| `openRecurringMasterDetail(masterId)` | 반복 관리 -> 마스터 상세 |
| `statusLabel(f)` | 상태 코드 -> 한글 라벨 매핑 |
| `filterInstancesByStatus(status)` | 상태별 필터 |
| `sortInstancesForStatus(items, status)` | 상태별 정렬 |

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 대상 | 결과 |
|------|------|------|
| 탭 클릭 | `.qtab` | 즐겨찾기/반복 관리 컨텐츠 전환 |
| 즐겨찾기 칩 클릭 | `.fav-chip` | 해당 퀘스트를 오늘에 복제, 토스트 "{제목} 오늘에 추가됨" |
| 반복 관리 아이템 클릭 | `.rec-manage-item` | 마스터 상세 뷰 진입 (detailFromRecurTab=true) |
| 상태 칩 클릭 | `.fchip` | 해당 상태 필터링, visibleCount 리셋 |
| 체크박스 클릭 | `.todo-chk` | 완료/미완료 토글 |
| 본문 영역 클릭 | `.todo-content` | 상세 뷰로 이동 |
| 별 아이콘 클릭 | `.todo-act-btn.star` | 즐겨찾기 on/off 토글 |
| 삭제 아이콘 클릭 | `.todo-act-btn.btn-danger` | confirm 후 삭제 |
| "새 퀘스트 추가" 클릭 | `.todo-add-btn` | 생성 폼 열기 |
| "더보기" 클릭 | `.load-more-btn` | 10건 추가 노출 |

## 6. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `quests-panel.md` | 퀘스트 시스템 전체 스펙, 데이터 모델, 상태 전이 |
| `quest-form.md` | 생성/편집 폼 (리스트에서 진입) |
| `quest-detail.md` | 상세 뷰 (리스트 아이템 클릭 시 진입) |
| `today-todo-overlay.md` | 완료 토글 시 동기화되는 오버레이 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 8.1 wiki 템플릿 기반 신규 작성 |

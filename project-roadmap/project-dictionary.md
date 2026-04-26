# RecordTales — 프로젝트 용어 사전 (Project Dictionary)

> **문서 성격**: 세계관 용어와 인게임 UI 명칭의 공식 사전.
> 다른 모든 문서는 이 문서의 용어를 기준으로 작성됩니다.
> 일반 작성 규칙은 `project-docs-guide.md` 참조.

---

## 📑 목차

- [0. 이 문서를 작성·수정하기 전에 반드시 읽을 것](#0-이-문서를-작성수정하기-전에-반드시-읽을-것)
  - [0.1. 핵심 원칙 — 모든 UI에는 진입 경로가 있다](#01-핵심-원칙--모든-ui에는-진입-경로가-있다)
  - [0.2. 작성 순서](#02-작성-순서-반드시-이-순서로)
  - [0.3. 애매한 UI의 배치 — 결정 규칙](#03-애매한-ui의-배치--결정-규칙)
  - [0.4. 완료 후 검증 체크리스트](#04-완료-후-검증-체크리스트)
  - [0.5. 예시 — 올바른 구조 vs 나쁜 예](#05-예시--올바른-구조-vs-나쁜-예)
- [1. 게임 시놉시스/세계관 관련 용어](#1-게임-시놉시스세계관-관련-용어)
  - [1.1. 세계관 요약](#11-세계관-요약)
  - [1.2. 핵심 용어 사전](#12-핵심-용어-사전)
  - [1.3. 현실 ↔ 세계관 은유 매핑](#13-현실--세계관-은유-매핑)
- [2. 인게임 UI 플로우 구조도 정의](#2-인게임-ui-플로우-구조도-정의)
  - [2.1. 전체 레이아웃 (Z자 섹션 지도)](#21-전체-레이아웃-z자-섹션-지도)
  - [2.2. 각 섹션별 UI 용어 정의](#22-각-섹션별-ui-용어-정의)
  - [2.3. 전역·트리거 기반 UI 요소](#23-전역트리거-기반-ui-요소)
- [3. 관련 문서](#3-관련-문서)

---

## 0. 이 문서를 작성·수정하기 전에 반드시 읽을 것

> **AI 에이전트 및 작성자는 이 섹션을 먼저 읽고 내용을 숙지한 뒤에만 이후 섹션을 수정한다.**

### 0.1. 핵심 원칙

이 사전은 **두 가지 핵심 원칙**을 따른다.

**① 용어 정의만 다룬다 (Scope)**

사전의 목적은 "이후 다른 기획서를 작성할 때 기준이 되는 **명칭·고유 명사** 를 정리"하는 것이다. 각 UI 요소의 **상세 동작 규칙·상태 전이·진입 조건 로직**은 사전의 범위가 **아니며** `approved/`, `draft/`, `project-wiki/` 등 해당 문서에서 정의한다.

- ✅ 사전에 적어야 할 것: 영문 명칭 / 한글 명칭 / 한 줄 정의 / 하위 명칭 / 상세 규칙이 정의된 문서 링크
- ❌ 사전에 적으면 안 되는 것: 진입 조건의 세부 로직 / 클릭 동작의 결과 / 상태 전이 규칙 / 갱신 주기 / 시각 스타일

이 원칙을 어기면 사전이 시스템 스펙을 중복 소유하게 되어 업데이트가 어긋난다.

**② 모든 UI에는 진입 경로가 있다 (Hierarchy)**

사용자가 어떤 UI를 보고 있다는 것은 **거기까지 오는 과정**이 있었다는 뜻이다. 따라서 UI 사전은 단어의 알파벳 나열이 아니라 **진입 경로 트리**로 구성한다.

- 상위 컨테이너 → 하위 요소 → 세부 상태 순서로 **중첩 목차**를 만든다
- 각 항목은 **상위에서 어떻게 도달하는가**를 전제로 배치된다
- "어디에 속하는지 애매한 UI"는 진입 경로를 먼저 따져서 위치를 결정한다 (0.3. 참조)

이 원칙을 어기면 같은 UI가 여러 곳에 중복 정의되거나, 특정 UI가 사전 어디에서도 찾기 어려워진다.

### 0.2. 작성 순서 (반드시 이 순서로)

1. **진입 경로 먼저 파악**한다 — 이 UI는 어떤 상위 요소에서, 어떤 조건으로 나타나는가?
2. **상위 목차에 배치**한다 — 진입의 출발점이 되는 상위 컨테이너 아래에 위치시킨다
3. **명칭과 정의를 기록**한다 — 영문 표기(kebab-case), 한글 명칭(있다면), 한 줄 정의
4. **하위 명칭을 나열**한다 — 이 요소를 구성하는 세부 UI의 kebab-case 명칭 목록 (있을 경우)
5. **상세 규칙 문서 링크**를 건다 — `approved/*.md`, `draft/*.md`, 또는 `html-design-style-guide.md` 등

**4~5에서 하지 말아야 할 것**: 해당 문서에서 다룰 동작 규칙·진입 조건 로직·상호작용 결과를 사전에 복사해 오는 것. 링크만 건다.

### 0.3. 애매한 UI의 배치 — 결정 규칙

어디에 속하는지 판단이 흔들리는 UI를 만났을 때, 아래 순서대로 적용한다.

| 판단 순서 | 질문 | 배치 |
|----------|-----|-----|
| 1 | **어느 공간에 시각적으로 고정**되어 있는가? | → 해당 공간 섹션(2.2.) 아래 |
| 2 | 고정 공간이 애매하면 — **어떤 시스템에 종속**되어 조건부로 나타나는가? | → 해당 시스템의 하위 |
| 3 | 특정 공간·시스템에 종속되지 않고 **트리거 기반으로만 등장**하는가? | → 용어 정의는 `2.3. 전역·트리거 기반 UI 요소`에, 상세 동작 규칙은 해당 `approved/` 문서에 |
| 4 | 모든 공간·시스템에 공통으로 쓰이는 **스타일·알림 요소**인가? | → `html-design-style-guide.md`로 위임 |

**예시 적용**:

- `session-mini`: 공간은 `schedule-nav-bar` 안이지만 **Focus 시스템의 조건부 확장** → Focus 버튼 하위
- `record-modal`: 특정 공간 없이 Focus 세션 종료로만 등장 → `2.3.`에 용어 정의 + `approved/record-modal.md`에 동작 규칙
- `toast`: 전역 알림 → style-guide로 위임

### 0.4. 완료 후 검증 체크리스트

작성·수정이 끝난 뒤 **반드시** 다음을 확인한다.

- [ ] 사전에 등장한 모든 UI 명칭이 **진입 경로 트리**에 정확히 한 곳에 위치하는가
- [ ] 같은 UI가 두 곳 이상에서 정의되어 있지 않은가 (중복 검사)
- [ ] 이번 추가·수정한 용어가 **승격 계기(draft → approved 또는 신규 기획)에서 발생한 고유 명사**인가 (아래 0.4.1. 참조)
- [ ] 각 UI에 **명칭 · 정의 · 하위 명칭(있다면) · 상세 규칙 문서 링크** 4요소가 모두 기술되었는가
- [ ] 애매한 UI의 배치가 0.3.의 결정 규칙을 따르는가

#### 0.4.1. 사전의 업데이트 철학 — **증분 업데이트(Incremental Update)**

이 사전은 **HTML 코드에 존재하는 모든 CSS 클래스를 선제적으로 전수 수록**하는 것이 **아닙니다**. 다음 시점에만 **증분 추가**됩니다.

- `draft/*.md`가 **`approved/*.md`로 승격**될 때, 그 시스템에 등장하는 **새로운 고유 명사**
- 새 기능·화면이 기획 단계에서 **공용으로 쓰이는 용어**가 필요하다고 판단될 때
- 기존 approved 시스템에서 **새 UI 요소가 추가**되어 다른 시스템도 참조할 여지가 생길 때

따라서:

- ✅ **approved/wiki가 담당하는 세부 UI 명칭**은 사전에 없어도 정상입니다 (각 approved 문서가 관리)
- ✅ **현재 HTML에 존재하지만 아직 어떤 문서에도 정의되지 않은 CSS 클래스**는 사전 추가 대상이 **아닙니다**
- ✅ 사전은 **"문서 간 공용 참조 용어만"** 수용합니다. 개별 시스템의 내부 명칭은 해당 시스템 문서의 영역입니다

### 0.5. 예시 — 올바른 구조 vs 나쁜 예

**올바른 예** (축약):

```
#### 2.2.2. schedule-nav-bar (우상)
- 정의: 시간 인식과 핵심 기능 진입을 제공하는 우상단 공간
- 코드상 컨테이너: `.tr-block`

**2.2.2.1. clock-plate**
- 정의: 현재 시간·날짜를 표시하는 상단 영역
- 하위 명칭: `clock-time`, `clock-date`
- 상세 규칙: `approved/clock.md`

**2.2.2.2. nav-bar**
- 정의: Focus/Quests/Archive 3버튼을 담는 바
- 상세 규칙: `approved/navigation-bar.md`

**2.2.2.2.1. Focus 버튼**
- 정의: Focus 모드(집중 기능) 진입 버튼
- 연동 UI — `session-mini`:
  - 정의: Focus 세션 중 nav-bar 좌측에 붙어 나타나는 세션 정보 위젯
  - 하위 명칭: `sm-indicator`, `sm-time`, `sm-phase`, `sm-dots`
  - 상세 규칙: `approved/focus-panel.md`
```

**나쁜 예** (원칙 위반):

```
#### 2.2.2. schedule-nav-bar
  2.2.2.1. clock-plate
  2.2.2.2. session-mini     ← Focus에 종속인데 독립 항목으로 배치됨
  2.2.2.3. nav-bar
```

session-mini는 nav-bar와 형제가 아니라 **Focus 버튼의 조건부 확장 UI**이므로 Focus 버튼 하위에 있어야 한다.

**또 다른 나쁜 예** — 규칙이 섞여 들어간 경우:

```
**2.2.2.1. clock-plate**
- 정의: 현재 시간·날짜 표시
- 진입 조건: 상시 표시       ← 진입 조건은 규칙 영역
- 클릭 동작: 없음             ← 상호작용은 규칙 영역
- 갱신 주기: 15초마다 체크    ← 동작 상세는 규칙 영역
```

사전은 **용어 정의**까지만 한다. 위의 "진입 조건", "클릭 동작", "갱신 주기"는 `approved/clock.md`에 기술되어야 한다.

---

## 1. 게임 시놉시스/세계관 관련 용어

### 1.1. 세계관 요약

RecordTales는 사용자가 자신의 하루를 **길드 운영**으로 프레이밍하는 집중 도구입니다. 사용자는 자신이 속한 길드의 **길드 마스터**이며, 하루의 할 일은 길드가 수행할 **퀘스트(Quest)**, 집중 시간은 **출정(Focus Session)**, 완료된 기록은 **아카이브(Archive)** 에 길드의 연대기로 누적됩니다.

세계관 철학(왜 이 은유를 쓰는가)은 `project-identity.md` 3.1. 참조. 이 섹션은 개별 용어의 **정의**에 집중합니다.

### 1.2. 핵심 용어 사전

| 용어 (영문) | 용어 (한글) | 정의 |
|-----------|-----------|------|
| Guild | 길드 | 사용자의 하루 관리 단위이자 은유적 조직. 1인 사용자 = 1길드 |
| Guild Master | 길드 마스터 | 사용자의 역할. 고정됨 |
| Grimhollow | 그림할로우 | 현재 프로토타입의 기본 길드명 (디자인 확인용 더미). 최종 제품에서는 튜토리얼에서 사용자가 설정 |
| Quest | 퀘스트 | 할 일 하나를 가리키는 세계관 명칭. "할 일"과 공식적으로 병용 |
| Single Quest | 단발 퀘스트 | 일회성으로 수행하는 퀘스트. `recurrence: 'none'` |
| Recurring Quest | 반복 퀘스트 | 주기적으로 발행되는 퀘스트. **마스터 + 인스턴스** 구조 |
| Master | 마스터 | 반복 퀘스트의 **템플릿 객체**. `isRecurringMaster: true` |
| Instance | 인스턴스 | 마스터로부터 자정 롤오버 시 생성되는 **실제 물리 객체**. 단발 퀘스트와 동일 스키마 |
| Favorite | 즐겨찾기 | 자주 쓰는 퀘스트 표시. `isFavorite: true` |
| Archive | 아카이브 | 완료된 퀘스트와 Focus 기록의 보관소. 길드의 연대기 |
| Record | 기록 | 아카이브에 저장되는 단위. Focus 세션 완료 또는 퀘스트 완료 시 생성 |
| Focus | 집중 (기능명) | `schedule-nav-bar`의 Focus 버튼으로 접근하는 기능 영역. 타이머와 스톱워치 제공 |
| Focus Session | 출정 (세션명) | 집중 타이머 또는 스톱워치로 측정되는 한 번의 집중 시간 단위 |
| Sigil | 시길 | 길드 문장. `focus-main-display` 중앙의 시각 요소 |
| Motto | 모토 | `"Per focum, ad gloriam — 집중으로, 영광으로"`. 길드의 표어 |
| Anno Magii | 안노 마기 | "마법의 해"를 뜻하는 라틴어 표기. 현재 `MMXXVI` (2026) |
| Category | 카테고리 | 퀘스트·기록의 분류 태그. 사용자 정의 가능, 최대 12개 |
| Command Post | 커맨드 포스트 | `guild-menu-panel`의 세계관 별칭. 길드 운영 명령 영역 |
| Suspend / Suspended | 중단 | 진행 중이던 퀘스트를 일시 멈춘 상태. 사유 입력 가능 |
| Overdue | 지연 | 마감일이 지났으나 완료되지 않은 상태. 빨간 배지로 표시 |
| Rollover | 롤오버 | 자정에 일어나는 상태 전환. 마스터로부터 새 인스턴스 생성, 지연 상태 계산 등 수행 |

### 1.3. 현실 ↔ 세계관 은유 매핑

| 현실 개념 | 세계관 용어 | 코드·UI 반영 |
|---------|-----------|------------|
| 사용자 | Guild Master | 좌상 `daily-status-panel`에 역할 표시 |
| 하루 | 길드의 하루 운영 | 자정 롤오버가 하루의 경계 |
| 할 일 (일회성) | Single Quest | `recurrence: 'none'` 객체 |
| 반복 할 일 | Recurring Quest (Master) | 템플릿 1개 + 매일 인스턴스 발행 |
| 개별 반복 일정 | Instance | 특정 날짜의 실제 실행 단위, 독립 ID·마감일 보유 |
| 완료한 기록 | Archive / Record | 우상 `schedule-nav-bar`의 Archive 버튼에서 조회 |
| 집중 시간 | Focus Session / 출정 | 25분 사이클 기반 타이머 + 자유 측정 스톱워치 |
| 일시 중단 | Suspended | 포기하지 않고 잠시 멈춤, 사유 기록 가능 |
| 기한 경과 | Overdue | 빨간 배지로 시각적 강조, `schedule-nav-bar`의 Quests 버튼에도 점 표시 |
| 자주 하는 일 | Favorite | Quests 패널 상단 즐겨찾기 행 |
| 분류 태그 | Category | 최대 12개, 사용자 정의 |
| 운영 명령 메뉴 | Command Post | 우하 `guild-menu-panel` 3버튼 영역 |
| 길드 문장·표어 | Sigil / Motto | 중앙 `focus-main-display` 상시 표시 |

---

## 2. 인게임 UI 플로우 구조도 정의

> **사전의 범위 재확인**: 이 섹션은 UI 요소의 **명칭·정의·하위 명칭·상세 규칙 문서 링크**만 다룹니다.
> - 동작 규칙·상태 전이·진입 조건 로직·시각 스타일은 **링크된 문서에서 정의**됩니다.
> - 섹션을 넘나드는 **시스템 흐름**은 각 `approved/` 문서 내부의 흐름 섹션에 기술됩니다 (이 사전에서는 다루지 않음).
> - 대부분의 `approved/` 문서는 현재 **작성 전** 상태이므로, 링크가 빈 참조일 수 있습니다.
> - 링크된 문서는 작업 진행에 따라 **이동·통합·분할될 수 있습니다**. 링크가 깨지면 `approved/` 또는 `project-wiki/`를 재탐색하세요.

### 2.1. 전체 레이아웃 (Z자 섹션 지도)

1920×1080 화면의 6개 영역 배치:

```
┌───────────────────────────────┬────────────────────────────────┐
│ [좌상] daily-status-panel     │ [우상] schedule-nav-bar        │
│   · guild-plate               │   · clock-plate                │
│   · todo-overlay              │   · nav-bar                    │
│                               │     - Focus / Quests / Archive │
│                               │     + session-mini (조건부)    │
│                               │                                │
│            ┌──────────────────────────────────┐                │
│            │ [중앙] focus-main-display        │                │
│            │   · sigil                        │                │
│            │   · particles                    │                │
│            └──────────────────────────────────┘                │
│                                                                │
│ [좌하] audio-player-panel     │ [우하] guild-menu-panel        │
│   · music-player              │   · command-post (3버튼)       │
│   · ambient-btn               │     - 길드원 모집              │
│     + ambient-overlay (조건부)│     - 스쿼드 편성              │
│                               │     - 환경 설정                │
└───────────────────────────────┴────────────────────────────────┘

                ┌─────────────────────────────────┐
                │ [오버레이] detail-side-panel    │  ← nav-bar 버튼 클릭 시
                │   · Focus / Quests / Archive    │     화면 우측 고정 오픈
                │     3모드 중 하나만 표시        │
                └─────────────────────────────────┘

                ┌─────────────────────────────────┐
                │ [전역 모달] record-modal        │  ← Focus 세션 종료 시
                │                                 │     화면 중앙 팝업
                └─────────────────────────────────┘
```

**읽기·작성 순서**: 좌상 → 우상 → 좌하 → 우하 → 중앙 → 사이드 (Z자 흐름)

**레이어 관계** (용어 정의 차원에서만 기록):
- 6개 영역 → 기본 레이어
- `detail-side-panel` → 중간 오버레이 레이어
- `record-modal` → 최상위 모달 레이어

---

### 2.2. 각 섹션별 UI 용어 정의

#### 2.2.1. daily-status-panel (좌상)

- **정의**: 사용자의 길드 마스터 정체성과 오늘 할 일을 표시하는 좌상단 공간
- **코드상 컨테이너**: `.tl-block`

**2.2.1.1. guild-plate**

- 정의: 길드 정체성(문장·이름·레벨·역할)을 표시하는 플레이트
- 하위 명칭: `guild-crest`, `guild-info`, `guild-name`, `guild-lvl-row`, `guild-lvl`, `guild-sub`
- 상세 규칙: `approved/guild-info.md`

**2.2.1.2. todo-overlay**

- 정의: 오늘 수행 예정 퀘스트를 표시하는 접이식 오버레이
- 하위 명칭: `todo-hdr`, `todo-hdr-left`, `todo-dot`, `todo-hdr-title`, `todo-toggle-btn`, `todo-body-list`, `tl-todo-item`, `tl-todo-chk`, `tl-todo-text`, `tl-todo-cat`
- 상세 규칙: `approved/today-todo-overlay.md`

---

#### 2.2.2. schedule-nav-bar (우상)

- **정의**: 시간 인식과 핵심 기능 진입(Focus / Quests / Archive)을 제공하는 우상단 공간
- **코드상 컨테이너**: `.tr-block`

**2.2.2.1. clock-plate**

- 정의: 현재 시간과 날짜를 표시하는 상단 영역
- 하위 명칭: `clock-time`, `clock-date`
- 상세 규칙: `approved/clock.md`

**2.2.2.2. nav-bar**

- 정의: Focus / Quests / Archive 3개 모드 진입 버튼을 담는 바
- 코드상 컨테이너: `.nav-bar`
- 하위 명칭 (공통): `nav-btn`, `nav-btn-label`
- 상세 규칙: `approved/navigation-bar.md`

**2.2.2.2.1. Focus 버튼**

- 정의: Focus 모드(집중 기능) 진입 버튼
- 식별색: 코랄 계열 (`--focus-c` = `#e8a87c`)
- 연동 UI — **`session-mini`** (Focus 세션 조건부 확장):
  - 정의: Focus 세션 진행 중 + 패널 닫힘 상태에서 nav-bar 좌측에 붙어 나타나는 세션 정보 위젯
  - 하위 명칭: `sm-indicator`, `sm-body`, `sm-time`, `sm-phase`, `sm-dots`, `sm-dot`
  - 상세 규칙: `approved/focus-panel.md`

**2.2.2.2.2. Quests 버튼**

- 정의: Quests 모드(퀘스트 관리) 진입 버튼
- 식별색: 골드 계열 (`--todo-c`)
- 연동 UI — **`nav-dot`** (지연 알림 도트):
  - 정의: Quests 버튼 우측 상단에 미완료·지연 퀘스트가 있음을 알리는 빨간 점
  - 상세 규칙: 표시 조건 로직은 `approved/quests-panel.md`, 배치·스타일은 `approved/navigation-bar.md`

**2.2.2.2.3. Archive 버튼**

- 정의: Archive 모드(기록 조회) 진입 버튼
- 식별색: 라벤더 계열 (`--diary-c`)
- 연동 UI: 없음

---

#### 2.2.3. audio-player-panel (좌하)

- **정의**: 작업 중 청각 환경을 조성하는 좌하단 공간. 배경 음악과 환경음 제공
- **코드상 컨테이너**: `.bl-block`

**2.2.3.1. music-player**

- 정의: 사전 정의된 배경 음악 트랙을 재생하는 플레이어
- 세계관 명칭: "Bard's Refrain"
- 하위 명칭:
  - 헤더: `pl-hdr`, `pl-ornament`, `pl-label`, `pl-list-btn`
  - 트랙 리스트 (조건부): `player-tracks`, `tl-hdr-label`, `tl-item`, `tl-icon`, `tl-body`, `tl-track-title`, `tl-track-sub`, `tl-dur`
  - 현재 곡: `pl-track-row`, `pl-art`, `pl-art-glyph`, `pl-meta`, `pl-title`, `pl-sub`, `eq-bars`, `eq-bar`
  - 진행 바: `pl-progress`, `p-time`, `p-bar`, `p-fill`
  - 컨트롤: `pl-controls`, `pb`, `pb.play`, `vol-row`, `vol-icon`, `vol-track`, `vol-fill`
- 상세 규칙: `approved/music-player.md`

**2.2.3.2. ambient-btn + ambient-overlay**

- 정의: 환경음(빗소리·바람·불·숲·바다) 믹서. 버튼 클릭 시 오버레이가 펼쳐짐
- 세계관 명칭: "Ambient Soundscape"
- 하위 명칭:
  - 버튼: `ambient-btn`
  - 오버레이 (조건부): `ambient-overlay`, `ambient-inner`, `ambient-title`
  - 개별 사운드 행: `amb-row`, `amb-icon`, `amb-name`, `amb-track`, `amb-fill`, `amb-val`
- 상세 규칙: `approved/ambient-sound.md`

---

#### 2.2.4. guild-menu-panel (우하)

- **정의**: 길드 운영 메타 명령(길드원 모집·스쿼드 편성·환경 설정)을 제공하는 우하단 공간. 현재 3버튼 정적 배치
- **코드상 컨테이너**: `.br-block`

**2.2.4.1. command-post**

- 정의: 3개의 길드 운영 명령 버튼을 담는 정적 메뉴 영역
- 세계관 명칭: "Command Post"
- 하위 명칭: `cmd-label`, `cmd-btns`, `cmd-btn`, `cmd-btn-label`
- 상세 규칙 (컨테이너): `approved/command-post.md`
- 각 버튼의 기능 규칙 (draft 단계, 현재 클릭 시 동작 없음):
  - 길드원 모집 버튼 → `guild-member-recruit.md`
  - 스쿼드 편성 버튼 → `draft/squad-formation.md`
  - 환경 설정 버튼 → `draft/environment-settings.md`

---

#### 2.2.5. focus-main-display (중앙)

- **정의**: 화면 중앙 시각 중심축. 기능 UI가 배치되지 않는 "집중의 영역". 시길과 파티클만 표시
- **코드상 컨테이너**: `.center-visual`

**2.2.5.1. sigil**

- 정의: 길드 문장. 고리·글자·모토·연도 표기로 구성된 중앙 시각 요소
- 하위 명칭: `sigil-ring`, `sigil-core`, `sigil-anno`, `sigil-letter`, `sigil-motto`, `sigil-trans`
- 상세 규칙: 별도 시스템 문서 없음. 시각 스타일은 `html-design-style-guide.md`

**2.2.5.2. particles**

- 정의: 배경에 부유하는 장식용 파티클
- 하위 명칭: `particle`
- 상세 규칙: 별도 시스템 문서 없음. 시각 스타일은 `html-design-style-guide.md`

---

#### 2.2.6. detail-side-panel (사이드)

- **정의**: Focus / Quests / Archive 3모드 중 하나를 확장 표시하는 우측 고정 오버레이 패널. 3모드가 같은 공간을 공유하며 동시에 하나만 열림
- **코드상 컨테이너**: `.side-panel`
- **진입 경로**: `schedule-nav-bar`의 Focus / Quests / Archive 버튼 클릭
- **※ 용어 재검토 예정**: `detail-side-panel`이라는 이름은 향후 용도 확장 시 수정될 수 있음 (참조: `project-docs-guide.md` 5.)

**2.2.6.1. sp-hdr**

- 정의: 현재 모드 이름과 닫기 버튼을 포함하는 상단 헤더
- 하위 명칭: `sp-hdr-left`, `sp-kicker`, `sp-title`, `sp-close`

**2.2.6.2. sp-body**

- 정의: 활성 모드의 콘텐츠가 렌더링되는 본문 영역

**2.2.6.3. panel-backdrop**

- 정의: 패널이 열릴 때 나머지 화면을 어둡게 만드는 백드롭 레이어
- 진입 경로: `detail-side-panel`이 열림과 동시에 활성화
- 상세 규칙: `detail-side-panel` 전용. 별도 시스템 문서 없음

**2.2.6.4. Focus 모드**

- 정의: `detail-side-panel`에 표시되는 집중 기능(타이머·스톱워치) 콘텐츠
- 상세 규칙: `approved/focus-panel.md`

**2.2.6.5. Quests 모드**

- 정의: `detail-side-panel`에 표시되는 퀘스트 관리 콘텐츠
- 상세 규칙: `approved/quests-panel.md`

**2.2.6.6. Archive 모드**

- 정의: `detail-side-panel`에 표시되는 기록 조회 콘텐츠
- 상세 규칙: `approved/archive-panel.md`

---

### 2.3. 전역·트리거 기반 UI 요소

고정된 섹션에 속하지 않고 **특정 트리거로만 등장**하는 UI 요소의 **용어 정의**. 동작 규칙은 각 `approved/` 문서 참조.

**2.3.1. record-modal**

- 정의: Focus 세션 완료·종료 시 화면 중앙에 나타나는 기록 저장용 모달
- 트리거: Focus 타이머 사이클 완료 / 타이머 중도 종료 / 스톱워치 종료
- 하위 명칭:
  - 컨테이너: `rec-modal-wrap`, `rec-modal`
  - 헤더: `rm-icon`, `rm-title`, `rm-sub`, `rm-stat`, `rm-stat-label`, `rm-stat-val`
  - 카테고리 섹션: `sec-label`, `cat-chips-wrap`, `cat-chip`, `cat-chip-del`, `cat-add-row`, `cat-add-input`, `cat-add-btn`
  - 메모: `form-textarea`
  - 액션: `rm-actions`, `btn-rm-skip`, `btn-rm-save`
- 상세 규칙: `approved/record-modal.md`

---


## 3. 관련 문서

| 문서 | 다루는 것 |
|------|---------|
| `project-docs-guide.md` | 모든 문서가 따르는 공통 작성 규칙 |
| `project-identity.md` | 프로젝트 정체성, UX 원칙, HTML 작업 원칙, 세계관 철학 |
| `project-data.md` | 데이터 스키마 상세 (Quest, Master, Instance 필드 정의) |
| `html-design-style-guide.md` | 디자인 토큰, 색상, 타이포그래피, 전역 스타일 요소(`toast` 등) |
| `project-wiki/` | 각 시스템의 상세 설계 (시스템 계층 구조로 정리) |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성. 섹션 0(작성 원칙), 섹션 1(세계관 용어) 포함. 섹션 2(UI 플로우)는 자리 표시만, 다음 단계에서 작성 |
| 2026-04-24 | 2차 추가 — 섹션 2.1.(전체 레이아웃 Z자 지도) + 섹션 2.2.1. ~ 2.2.4.(daily-status-panel, schedule-nav-bar, audio-player-panel, guild-menu-panel) 작성. 섹션 2.2.5., 2.2.6., 2.3.은 다음 단계 |
| 2026-04-24 | 3차 수정 — 사전 범위 재정의. 섹션 2 전체를 **용어 정의 수준으로 전면 축소** (동작 규칙·상태 전이·진입 조건 로직 제거). 각 요소에 `approved/*.md` 상세 규칙 문서 링크 추가. 섹션 2.2.5.(focus-main-display), 2.2.6.(detail-side-panel) 추가. 섹션 2.3.은 "시스템 흐름"이 아닌 "전역·트리거 기반 UI 요소의 용어 정의"로 재정의 (record-modal 수용). 0.1. 핵심 원칙을 Scope+Hierarchy 2개로 확장, 0.2. 작성 순서 업데이트, 0.3./0.4./0.5.도 새 범위에 맞게 수정 |
| 2026-04-24 | 4차 수정 — (1) 2.2.2.2.1. Focus 버튼 식별색을 "민트 계열" → "**코랄 계열** (`#e8a87c`)"로 정정. 실제 HTML의 `--focus-c` 값 반영. (2) `design-style-guide.md` 파일명 변경에 따라 모든 참조를 `html-design-style-guide.md`로 일괄 교체 |
| 2026-04-24 | 5차 수정 — 0.4. 체크리스트 "누락 검사" 조항을 **"증분 업데이트 철학"**으로 재정의. 사전은 HTML 전수 수록이 아닌 draft→approved 승격·공용 용어 발생 시점에만 증분 추가하며, approved/wiki 문서가 담당하는 세부 UI 명칭은 사전 대상이 아님을 0.4.1.에 명시 |

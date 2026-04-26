# RecordTales 프로젝트 — 작업 인수인계 문서 (v2)

> **문서 목적**: 새 Claude 대화로 작업을 이어갈 때, 이전 대화의 맥락·결정 사항·미결 사항을 빠르게 파악하기 위한 인수인계 문서
> **작성일**: 2026-04-24 (v2, 2차 갱신)
> **현재 상태**: 공용 문서 4종 완성 + draft 시스템 2종 작성. **approved 문서 작성은 HTML 리팩터 이후로 보류**
> **이전 버전**: v1은 "레이아웃 용어 확정 단계에서 중단" 상태였음 — 이 문서로 완전 대체

---

## 📌 0. 10초 요약

- **RecordTales**: 길드 운영 은유의 데스크톱 집중 도구. 최종은 **언리얼 엔진**, 현재는 **HTML 디자인 프로토타입**
- 공용 문서 4종(`project-roadmap/`)과 draft 시스템 2종 완성
- **approved 문서 작성은 보류** — HTML 리팩터가 어느 정도 진행된 뒤 재개 예정
- 다음 세션: (ⓐ) HTML 리팩터 진행 후 approved 재개 또는 (ⓑ) draft 추가·수정·기타 문서 작업

---

## 📌 1. 프로젝트 정체성

### 1.1. 기본 정보

| 속성 | 값 |
|------|-----|
| **이름** | RecordTales |
| **최종 형태** | 언리얼 엔진 기반 데스크톱 애플리케이션 |
| **현재 단계** | HTML 디자인 프로토타입 (`guild-master-desktop.html`) |
| **해상도** | 1920×1080 고정 |
| **플랫폼** | PC (언리얼 엔진 기반) |
| **저장소** | 설치한 사용자의 로컬 스토리지 (현재는 메모리, HTML 단계 내에서 영속화 전환 예정) |
| **네트워크** | 현재 없음. 향후 Steam 연동 여지 |

### 1.2. 구조 한눈에 보기

- 4-코너 레이아웃 + 중앙 + 사이드 패널
- 좌상: 길드 정보 + 오늘의 퀘스트
- 우상: 시계 + Focus/Quests/Archive 진입 버튼
- 좌하: 음악 플레이어 + 환경음
- 우하: 커맨드 포스트 (draft 단계 3버튼)
- 중앙: 시길 + 파티클 (기능 UI 배치 금지)
- 사이드: Focus/Quests/Archive 확장 콘텐츠

### 1.3. 핵심 세계관

- 사용자 = 길드 마스터
- 할 일 = 퀘스트 (단발/반복)
- 완료 기록 = 아카이브 (길드 연대기)
- 집중 시간 = Focus 세션 (출정)
- 모토: `"Per focum, ad gloriam — 집중으로, 영광으로"`

---

## 📌 2. 현재 폴더 구조

```
project-roadmap/                    ← 프로젝트 불변 방향성 문서 4종
├── project-docs-guide.md           302줄. 공통 작성 규칙
├── project-identity.md             163줄. 정체성·UX 원칙·방향성
├── project-dictionary.md           474줄. 용어 사전 (증분 업데이트)
└── html-design-style-guide.md      532줄. HTML 시각 기준 + 공용 컴포넌트

project-wiki/                         ← 게임 시스템 계층 구조 기반 문서 정리
├── guild/                            ← 길드 시스템
│   ├── ui/guild-info.md              길드 정보 UI 스펙
│   ├── ui/command-post.md            길드 메뉴 UI
│   ├── ui/sigil.md                   길드 문장 비주얼
│   ├── guild-member-recruit.md       길드원 모집 (approved)
│   ├── draft/squad-formation.md      스쿼드 편성 (draft)
│   └── guild-member/draft/guild-member.md  길드원 시스템 (draft)
├── focus/focus-panel.md              집중 시스템 스펙
├── quests/quests-panel.md            퀘스트 시스템 스펙
├── archive/archive-panel.md          아카이브 시스템 스펙
├── audio/                            오디오 시스템
├── experience/draft/                 경험치 시스템 (draft)
├── tutorial/draft/                   튜토리얼 (draft)
├── settings/draft/                   환경 설정 (draft)
└── ui/                               글로벌 UI 요소
├── focus-panel.md
├── record-modal.md
├── quests-panel.md                 ← 리팩터 중심, XL
├── archive-panel.md
├── music-player.md
├── ambient-sound.md
└── command-post.md
```

---

## 📌 3. 공용 문서 4종 — 각 책임

### 3.1. project-docs-guide.md

모든 문서가 따라야 할 공통 규칙. **가장 먼저 읽어야 함**.

주요 규칙:
- 파일명 kebab-case
- 모든 번호 뒤에 `.` 필수 (예: `1.`, `1.1.`, `1.1.1.`)
- 다이어그램: Mermaid(상태/ERD/플로우) + ASCII(레이아웃)
- 한글 본문 + 영문 병기
- 레이아웃 영역 용어 6종 확정 (5. 섹션에 표로 정의)
- 시스템 문서 템플릿 3종 (approved/draft/wiki, 8. 섹션)
- **문서별 특수 원칙은 해당 문서 최상단 섹션 `0.`에 배치** (6.5.)

### 3.2. project-identity.md

프로젝트의 불변 방향성. 철학·UX 원칙·미래 방향성.

- 1. 한 줄 정의
- 2. 제품 정체성 (플랫폼·저장소·대상 사용자)
- 3. 세계관 (길드 은유 매핑, 기본 길드 설정)
- 4. UX 원칙 (5가지)
- 5. 미래 방향성 (단기·중기·장기)
- 6. 관련 문서

**중요**: HTML 작업 제약은 이 문서에 **없음**. `html-design-style-guide.md` 1. 섹션으로 이전됨.

### 3.3. project-dictionary.md

UI 요소·세계관 용어 사전.

- 0. 작성 원칙 (**매우 중요**)
  - 0.1. 두 핵심 원칙: **Scope** (용어 정의만) + **Hierarchy** (진입 경로 트리)
  - 0.4.1. **증분 업데이트 철학** — HTML 전수 수록 아님, draft→approved 승격 시점에만 공용 용어 추가
- 1. 세계관 용어 (21개)
- 2. 인게임 UI 플로우 (6개 섹션별 용어 + 전역 UI)

**중요**: 각 UI 용어는 **명칭·정의·하위 명칭·상세 규칙 문서 링크**만 다루고, 동작 규칙은 `approved/`에 위임.

### 3.4. html-design-style-guide.md

HTML 프로토타입 전용 시각 기준 + 작업 제약.

- 0. 작성 원칙 (Scope, 토큰 추가 원칙, 언리얼 전환 관점)
- 1. HTML 프로토타입 작업 제약 (1920×1080, 단일 파일, 외부 의존성 등)
- 2. 색상 (배경/텍스트/기능 식별색/상태색/골드/테두리)
- 3. 타이포그래피 (4종 폰트, 용도 매핑)
- 4. 모서리·간격
- 5. 모션
- 6. 표면 효과 (글래스·스테이지 그라데이션·그림자)
- 7. 아이콘
- 8. 공용 UI 컴포넌트 (버튼 5종·입력·토글·칩 + 사용 원칙 4가지)
- 9. 전역 UI 요소 (toast)

---

## 📌 4. draft 시스템 2종

### 4.1. tutorial-flow.md
- 최초 실행 시 3~5 스텝 서사적 튜토리얼
- 길드명 설정 → 첫 퀘스트 작성 → Sigil 계시 연출
- 8개 열린 질문 (스킵 가능 여부 등)

### 4.2. experience-system.md
- RPG 길드 성장 은유 (XP·레벨)
- XP 획득 경로 초안 + `daily-status-panel` XP 게이지 UI 스케치
- 10개 열린 질문 (XP 공식·레벨 곡선·해금 시스템 등)

### 4.3. 미작성 draft 3종 (handoff-spec v1에 언급)
- guild-member-hiring / squad-formation / environment-settings
- 파일 미생성 상태. 필요 시 생성

---

## 📌 5. approved 폴더 — 첫 시도 + 보류 상태

### 5.1. guild-info.md (첫 시도, 참고용 유지)

- 246줄의 완성된 초안
- approved 문서 템플릿 9개 섹션 모두 채움
- **보류 이유**: 사용자 판단 — "HTML 파일이 어느 정도 수정된 후 다시 요청" 방침

**이 문서가 제시한 스타일 기준** (나머지 10개 작성 시 참고):
- 제목: `# Guild Info — 길드 정보 플레이트` 형식
- [현재]/[목표]/[공통] 태그 활용
- 구현 체크리스트 3단계 분할 (8.1. 문서 / 8.2. 코드 / 8.3. 외부 연동)
- 시스템명과 파일명 불일치 시 ⚠️ 경고 명시
- 섹션 수는 고정, 내용 길이만 조절

### 5.2. 보류된 approved 작성 계획

**4개 그룹으로 재편**(이번 세션 후반부 결정):

| 그룹 | 시스템 | 비고 |
|------|--------|------|
| A. 좌상 | `guild-info`(첫 시도됨) + `today-todo-overlay` | |
| B. 우상 + 사이드 확장 | `clock` + `navigation-bar` + `focus-panel` + `record-modal`* + `quests-panel` + `archive-panel` | 가장 큼, quests가 리팩터 중심 |
| C. 좌하 | `music-player` + `ambient-sound` | |
| D. 우하 | `command-post` | draft 3종 연동 참조 |

*record-modal은 파일은 분리 유지하되 그룹 분류상 focus-panel 하위로 편입

**작성 순서 (확정)**: 영역 순차 (A → B → C → D) = Z자

---

## 📌 6. 이번 세션(2026-04-24) 주요 작업 내역

### 6.1. 처음에 받은 상태 (v1 handoff 기준)

- 레이아웃 용어 확정 단계에서 중단
- `RecordTales-spec.md` (62KB, v0.6) 통합 명세서 존재
- 명세서를 폴더 구조로 분리하기로 결정됨

### 6.2. 진행 순서별 주요 결정

1. **레이아웃 용어 6종 확정** (Z자 순서)
   - `daily-status-panel` (좌상)
   - `schedule-nav-bar` (우상)
   - `audio-player-panel` (좌하)
   - `guild-menu-panel` (우하)
   - `focus-main-display` (중앙)
   - `detail-side-panel` (사이드, ※ 재검토 예정)

2. **파일 구조 간소화**: `layout-terminology.md` 폐지 → 모든 용어를 `project-dictionary.md`에 통합

3. **공용 문서 4종 작성**: `project-docs-guide.md` → `project-identity.md` → `project-dictionary.md` → `html-design-style-guide.md`

4. **큰 구조 전환**: HTML = 최종 제품 → HTML = 언리얼 전 디자인 프로토타입
   - `project-identity.md` 전면 간결화
   - 작업 제약을 `html-design-style-guide.md`로 이관
   - 파일명 `design-style-guide.md` → `html-design-style-guide.md`

5. **dictionary 작성 원칙 3차 정교화**
   - 최초: 용어 + 규칙 혼재
   - 2차: 용어만, 진입 경로 트리, 시스템 흐름 섹션
   - 3차: 증분 업데이트 철학. HTML 전수 수록 아님, 승격 시점에만 추가

6. **draft 2종 추가**: `tutorial-flow.md`, `experience-system.md`

7. **공용 UI 컴포넌트 섹션 확장**: 버튼 5종·입력·토글·칩 규격화 → `html-design-style-guide.md` 8. 섹션

8. **approved 첫 시도**: `guild-info.md` → 이후 보류

### 6.3. 주요 수정·정정 이력

- **Focus 식별색 정정**: "민트" → "코랄" (`#e8a87c`). `--focus-c` 실제 값 반영
- **폰트 개수 정정**: "2종" → 실제 4종 (Cinzel, DM Serif Display, DM Mono, Noto Sans KR)
- **번호 규칙 강제**: 모든 문서에서 `1.`, `1.1.` 형식 (뒤 점 필수)

---

## 📌 7. 주요 작성 원칙 (절대 잊으면 안 될 것)

### 7.1. 문서 작성

- 모든 번호 뒤에 `.` 필수 (`## 1.`, `### 1.1.`)
- 파일명 kebab-case
- 한글 본문 + 영문 병기 (첫 등장 시)
- Mermaid(플로우/상태/ERD) + ASCII(레이아웃) 혼용
- 모든 문서 하단에 `## 📝 업데이트 이력` 필수

### 7.2. Dictionary 업데이트 원칙 (중요)

`project-dictionary.md`는 HTML 전수 수록이 **아님**. 다음 시점에만 증분 추가:
- draft → approved 승격 시 새 고유 명사
- 신규 공용 용어 필요 시
- approved에서 새 공용 참조 UI 추가 시

approved/wiki 담당 세부 UI는 사전에 없어도 정상.

### 7.3. 용어 일관성

- **레이아웃 영역 6종**: 항상 이 이름을 사용 (`daily-status-panel` 등)
- **Focus 기능명** vs **Focus Session 세션명** 구분
- **마스터** vs **인스턴스** 반복 퀘스트 모델 (리팩터 핵심)

### 7.4. 태그 (approved 문서용)

- `[현재]`: 현재 HTML 프로토타입 구현 상태
- `[목표]`: 리팩터 또는 향후 목표
- `[공통]`: 현재·미래 모두 해당

### 7.5. HTML 프로토타입 제약

`html-design-style-guide.md` 1. 참조. 특히:
- 1920×1080 고정
- 단일 HTML 파일 유지
- 외부 의존성은 Google Fonts만
- 중앙 영역(`focus-main-display`)에 기능 UI 배치 금지
- 영속화 가능 구조로 설계 (HTML 단계 내에서 전환 예정)

---

## 📌 8. 리팩터 설계 — 코드 작업 시 반영할 것

v1 handoff에서 이어진 리팩터 설계. 코드 리팩터 시점에 적용:

### 8.1. 반복 퀘스트 데이터 모델 전환

**이전**: 반복 인스턴스는 파생 계산
**목표**: 반복 인스턴스는 실제 물리 객체로 `A.todos`에 저장

핵심: **"반복 퀘스트는 단발성 퀘스트를 자동 생성하는 템플릿"**

### 8.2. 데이터 스키마 (통합 — 단발/마스터/인스턴스 동일)

```js
{
  id: string,                    // 13자리 YYYYMMDD+00001~99999
  title, description, category,
  recurrence: 'none' | 'daily' | 'weekly' | 'monthly',
  isRecurringMaster: boolean,
  masterId: string | null,
  startDate, deadline, recEndDate,
  deadlineOffset: number | null,
  doneAt, doneDate, suspendedAt, suspendReason,
  isFavorite,
  createdAt,
}
```

### 8.3. 상세 뷰 인라인 편집

- 편집 가능 필드는 텍스트 필드처럼 보이는 컨테이너
- Single-line: Enter=저장, Escape=취소, blur=저장
- Multi-line: Enter=저장, Shift/Ctrl+Enter=줄바꿈
- 캘린더/드롭다운: 선택 즉시 저장

### 8.4. 반복 제거 새 동작

`recEndDate` 변경 → **인스턴스 물리 삭제**로 전환
- 미래 선택: 선택일 이후 인스턴스 전부 삭제
- 오늘/과거 선택: 상태 태그 복수 선택 (지연/진행 예정/진행 중/완료/중단)
- 시작일 이전: 전체 삭제 (상태 태그 선택)
- **마스터는 어떤 경우에도 삭제되지 않음**

### 8.5. 절대 불변

- 퀘스트 **시작일**(`startDate`)은 절대 변경 불가 (ID 생성 기반)
- 반복 마스터의 **마감일 오프셋**(`deadlineOffset`)은 생성 후 불변
- 길드 **역할**은 `"Guild Master"` 고정

---

## 📌 9. 다음 세션에서 할 일 — 시나리오별

### 시나리오 A: HTML 리팩터 진행

현재 `guild-master-desktop.html` 2,786줄 기반으로 리팩터. 순서:
- R1. 데이터 모델 재작성 (섹션 8 참조)
- R2. 자정 롤오버 재작성 (실제 인스턴스 생성)
- R3. 생성 폼 업데이트 (`[−] N [+]` 오프셋 UI)
- R4. 상세 뷰 인라인 편집 구현
- R5. 반복 제거 새 동작
- R6. [삭제] 버튼 규칙 재정비
- R7. 리스트·알림 재동기화

### 시나리오 B: approved 문서 재개

사용자가 "HTML 어느 정도 리팩터된 뒤 다시 요청" 방침이므로, 리팩터 후 이 시나리오 진입.
- 작성 순서: 영역 순차 (A → B → C → D)
- `guild-info.md`는 재작성 시 **참고**로 활용
- 스타일 표준은 `guild-info.md` 가 제시한 것을 준수 (7.1. 참조)

### 시나리오 C: 다른 문서 작업

- draft 추가·수정 (미작성 3종)
- `project-data.md` 작성 (사용자가 "프로젝트 진행 후"로 미룸)
- `project-wiki/` 승격 결정

### 시나리오 D: 공용 문서 보완

현재 4종이 어느 정도 완성됐지만, 작업 중 발견된 개선 지점:
- `project-identity.md`에 섹션 0(불변 원칙) 추가 여부
- `html-design-style-guide.md`의 간격 토큰(`--space-*`) 도입 여부

---

## 📌 10. 현재 산출물 위치

`/mnt/user-data/outputs/` 폴더:

```
project-roadmap/
├── project-docs-guide.md
├── project-identity.md
├── project-dictionary.md
└── html-design-style-guide.md

project-wiki/                         (게임 시스템 계층 구조 기반)
├── guild/                            (길드 시스템 + 하위 시스템)
├── focus/                            (집중 시스템)
├── quests/                           (퀘스트 시스템)
└── ...                               (archive, audio, experience, tutorial, settings, ui)

handoff-spec.md (이 문서, v2)
record-tales-docs-2026-04-24-v2.zip (전체 스냅샷)
```

---

## 📌 11. 새 대화에서 처음 할 일

1. **이 문서(`handoff-spec.md` v2) 전체 읽기**
2. **`project-roadmap/` 4종 문서 내용 파악**
3. **`project-wiki/` 시스템별 문서 확인**
5. **사용자에게 다음 행동 물어보기** — 시나리오 A/B/C/D 중 어느 것을 할지

---

## 📌 12. 제거된 항목 (v1 → v2 변경점)

v1 handoff에서 있었지만 이번 세션에 폐지·변경된 것들:

- ❌ `layout-terminology.md` 계획 → 폐지. 용어는 `project-dictionary.md`에 통합
- ❌ 배치 순서 "공용 4종 → approved 핵심 4종 → approved 기타 7종 → draft + README" → 재편됨
- ❌ approved 순서 `quests-panel → archive-panel → focus-panel → record-modal` → 영역 순차로 변경
- ❌ `RecordTales-spec.md`(v0.6, 62KB) → 공용 문서들로 분해됨. 원본은 참조 전용

---

**끝. 이 문서를 새 대화 첫 메시지에 업로드하고 "이 문서 기준으로 작업 이어가주세요"라고 요청하면 맥락 복원됩니다.**

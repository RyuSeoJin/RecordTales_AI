# Project Wiki Folder Guide — 위키 폴더 구성 규칙

> **문서 성격**: `project-wiki/` 폴더의 **구성 원칙과 배치 규칙**을 정의합니다.
> 새 문서를 추가하거나 기존 문서를 이동할 때 이 가이드를 따릅니다.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 📑 목차

1. [기본 원칙](#1-기본-원칙)
2. [최상위 폴더 구조](#2-최상위-폴더-구조)
3. [시스템 폴더 내부 구조](#3-시스템-폴더-내부-구조)
4. [파일 배치 규칙](#4-파일-배치-규칙)
5. [하위 시스템 폴더](#5-하위-시스템-폴더)
6. [Draft → 승인 승격](#6-draft--승인-승격)
7. [현재 구조 스냅샷](#7-현재-구조-스냅샷)
8. [관련 문서](#8-관련-문서)

---

## 1. 기본 원칙

### 1.1. 게임 시스템 계층 구��

`project-wiki/`는 **게임 시스템 논리**를 기준으로 폴더를 구성한다. UI 패널 위치가 아닌, **해당 시스템이 게임 내에서 어떤 역할을 하는지**에 따라 분류한다.

### 1.2. 상위 → 하위 규칙

시스템이 속할 수 있는 **가장 큰 틀**의 폴더를 먼저 찾고, 그 안에서 하위로 세분화한다.

- 길드원 모집(`guild-member-recruit`)은 길드 시스템의 행동 → `guild/` 안에 배치
- 길드원 속성(`guild-member`)은 독립 하위 시스템 → `guild/guild-member/` 폴더로 분리

### 1.3. 문서 상태는 폴더가 아닌 헤더로 구분

문서가 draft인지 approved인지는 **문서 상단의 `> 문서 성격` 인용구**로 판별한다. 폴더 위치(루트 vs `draft/`)도 상태를 반영하지만, 최종 판단 기준은 문서 헤더이다.

---

## 2. 최상위 폴더 구조

각 최상위 폴더는 하나의 독립적인 게임 시스템을 대표한다.

| 폴더 | 시스템 | 설명 |
|------|--------|------|
| `guild/` | 길드 시스템 | 길드 정보, 길드원, 고용, 편성 등 길드 관련 전반 |
| `focus/` | 집중 시스템 | 타이머, 스톱워치, 집중 세션 |
| `quests/` | 퀘스트 시스템 | 할 일 관리, 반복 퀘스트, 카테고리 |
| `archive/` | 아카이브 시스템 | 캘린더 기반 기록 열람 |
| `audio/` | 오디오 시스템 | 배경 음악, 환경음 |
| `economy/` | 경제 시스템 | 골드 획득·소모 규칙 (크로스커팅) |
| `experience/` | 경험치 시스템 | 길드·길드원 레벨업, XP |
| `tutorial/` | 튜토리얼 시스템 | 최초 실행 온보딩 |
| `settings/` | 설정 시스템 | 테마, 볼륨, 시각 효과 |
| `ui/` | 글로벌 UI | 특정 시스템에 속하지 않는 공통 UI 요소 |

새 시스템이 기존 폴더에 속하지 않으면 최상위에 새 폴더를 생성한다.

---

## 3. 시스템 폴더 내부 구조

각 시스템 폴더는 최대 3종류의 하위 구조를 가진다.

```
{system}/
├── {system}.md          ← 승인된 시스템 스펙 (루트)
├── ui/                  ← UI 관련 스펙
│   └── {component}.md
├── draft/               ← 아이디어 단계 문서
│   └── {idea}.md
└── {sub-system}/        ← 하위 시스템 폴더 (필요 시)
    ├── {sub-system}.md
    ├── ui/
    └── draft/
```

| 위치 | 용도 | 문서 상태 |
|------|------|----------|
| 루트 (`{system}/`) | 시스템 핵심 스펙 | 승인(approved) |
| `ui/` | UI 컴포넌트 스펙 | 승인(approved) |
| `draft/` | 아이디어·초안 | 미승인(draft) |

### 3.1. 빈 폴더는 만들지 않는다

`ui/`나 `draft/`는 해당 문서가 존재할 때만 생성한다. 미래를 위해 미리 빈 폴더를 만들지 않는다.

---

## 4. 파일 배치 규칙

### 4.1. 시스템 스펙 (승인 문서)

시스템의 **전체 동작·데이터 모델·규칙**을 정의하는 문서. 시스템 폴더 **루트**에 배치한다.

```
focus/focus-panel.md
quests/quests-panel.md
archive/archive-panel.md
```

### 4.2. UI 스펙

특정 **UI 컴포넌트의 시각·레이아웃·인터랙션**을 정의하는 문서. 시스템 폴더의 `ui/`에 배치한다.

```
guild/ui/guild-info.md        ← 길드 정보 표시 UI
guild/ui/command-post.md      ← 길드 메뉴 UI
focus/ui/record-modal.md      ← 기록 모달 UI
```

### 4.3. Draft 문서

아이디어 단계 문서. 시스템 폴더의 `draft/`에 배치한다.

```
guild/draft/guild-member-recruit.md
experience/draft/experience-system.md
```

### 4.4. 글로벌 UI 요소

특정 시스템에 소속되지 않는 공통 UI는 최상위 `ui/` 폴더에 배치한다.

```
ui/clock.md                   ← 시계 표시
ui/navigation-bar.md          ← 모드 전환 바
ui/particles.md               ← 배경 파티클 효과
```

### 4.5. 배치 판단 기준

새 문서를 추가할 때 아래 순서로 위치를 결정한다.

1. **이 문서가 다루는 기능은 어떤 게임 시스템에 속하는가?** → 해당 시스템 폴더
2. **시스템 전체 스펙인가, UI 컴포넌트 스펙인가?** → 루트 vs `ui/`
3. **아이디어 단계인가, 승인된 스펙인가?** → `draft/` vs 루트 또는 `ui/`
4. **독립적으로 확장될 하위 시스템인가?** → 하위 시스템 폴더 생성 검토
5. **어느 시스템에도 속하지 않는 순수 UI인가?** → 최상위 `ui/`

---

## 5. 하위 시스템 폴더

하위 시스템은 상위 시스템 안에 **독립 폴더**로 존재한다. 향후 자체적으로 규칙·UI·draft가 추가될 수 있을 때 분리한다.

### 5.1. 분리 기준

- 해당 개념이 **자체적인 속성 체계**를 가진다 (예: 길드원의 등급·능력치·레벨)
- 하위 문서가 **2개 이상** 생길 것으로 예상된다 (예: guild-member.md + 향후 status 규칙 등)

### 5.2. 구조 예시

```
guild/
├── guild-member/              ← 하위 시스템
│   ├── guild-member.md        ← 승인 시 루트로 승격
│   ├── ui/                    ← 길드원 전용 UI 스펙
│   └── draft/                 ← 길드원 관련 draft
│       └── guild-member.md    ← 현재 아이디어 단계
```

### 5.3. 분리하지 않는 경우

단순한 **행동(action)**이나 **일회성 기능**은 하위 폴더를 만들지 않고 상위 시스템의 `draft/` 또는 루트에 직접 배치한다.

- `guild/draft/guild-member-recruit.md` — 길드에서 할 수 있는 행동
- `guild/draft/squad-formation.md` — 길드 마스터의 편성 행동

---

## 6. Draft → 승인 승격

### 6.1. 승격 절차

1. `draft/` 폴더에 있는 문서가 개발 착수 결정되면 승격
2. 문서를 `draft/`에서 **시스템 폴더 루트** (또는 `ui/`)로 이동
3. 문서 상단 `> 문서 성격` 인용구를 승인 문서 형식으로 변경
4. 필요 시 `project-docs-guide.md` 8.1. 템플릿에 맞게 구조 보강

### 6.2. 예시

```
# 승격 전
guild/draft/guild-member-recruit.md
> **문서 성격**: 아이디어 단계. 승인 전(draft).

# 승격 후
guild/guild-member-recruit.md
> **문서 성격**: `guild` 시스템의 **길드원 모집** 시스템 스펙.
```

---

## 7. 현재 구조 스냅샷

```
project-wiki/
├── guild/
│   ├── guild-member-recruit.md
│   ├── ui/
│   │   ├── guild-info.md
│   │   ├── command-post.md
│   │   └── sigil.md
│   ├── draft/
│   │   └── squad-formation.md
│   └── guild-member/
│       └── guild-member.md
├── focus/
│   ├── focus-panel.md
│   └── ui/
│       ├── timer.md
│       ├── stopwatch.md
│       ├── record-modal.md
│       └── session-mini.md
├── quests/
│   ├── quests-panel.md
│   └── ui/
│       ├── quest-list.md
│       ├── quest-form.md
│       ├── quest-detail.md
│       ├── recurrence-removal.md
│       └── today-todo-overlay.md
├── archive/
│   └── archive-panel.md
├── audio/
│   ├── music-player.md
│   └── ambient-sound.md
├── economy/
│   └── gold-economy.md
├── experience/
│   └── draft/
│       └── experience-system.md
├── tutorial/
│   └── draft/
│       └── tutorial-flow.md
├── settings/
│   └── draft/
│       └── environment-settings.md
└── ui/
    ├── clock.md
    ├── navigation-bar.md
    ├── particles.md
    ├── panel-system.md
    ├── toast.md
    └── category-modal.md
```

※ 이 스냅샷은 2026-04-26 기준이며, 시스템 추가·승격에 따라 변경됩니다.


---

## 8. 관련 문서

| 문서 | 다루는 것 |
|------|---------|
| `project-docs-guide.md` | 파일 명명, 번호 표기, 템플릿 등 공통 작성 규칙 |
| `project-identity.md` | 프로젝트 정체성, UX 원칙, 로드맵 방향성 |
| `project-dictionary.md` | 레이아웃 영역 용어, UI/기능/세계관 용어 사전 |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 초안 작성. project-plan/ → project-wiki/ 재편에 따른 폴더 구성 규칙 정의. |
| 2026-04-26 | economy/ 폴더 추가 (골드 경제 시스템). §2 테이블 및 §7 스냅샷 반영. |
| 2026-04-26 | gold-economy, guild-member-recruit, guild-member 승인 승격 반영. §7 스냅샷 업데이트. |

# Command Post

> **문서 성격**: `Guild` 시스템의 **Command Post** UI 컴포넌트 스펙.
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

- **한 줄 정의**: 길드원 모집, 길드원 관리, 해금 버튼을 제공하는 커맨드 포스트 영역
- **위치**: 우하단 영역 (`guild-menu-panel`) — `.br-block` 내부
- **구현 상태**: ✅ 구현 완료

---

## 2. UI 구조

### 2.1. 와이어프레임

```
                          ┌─ .br-block (bottom:28px, right:36px) ─┐
                          │                                        │
                          │ ───── Command Post   (.cmd-label)      │
                          │                                        │
                          │ ┌─ .cmd-btns ────────────────────────┐ │
                          │ │ ┌──────────┐┌──────────┐┌────────┐ │ │
                          │ │ │ 👤+      ││ ★        ││ 🔓     │ │ │
                          │ │ │          ││          ││        │ │ │
                          │ │ │ 길드원   ││ 길드원   ││  해금  │ │ │
                          │ │ │  모집    ││  관리    ││        │ │ │
                          │ │ │  96x96   ││  96x96   ││ 96x96  │ │ │
                          │ │ └──────────┘└──────────┘└────────┘ │ │
                          │ └────────────────────────────────────┘ │
                          └────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.br-block (position:absolute, bottom:28px, right:36px, z-index:20)
├── .cmd-label (섹션 라벨 + ::before 라인 데코레이션)
└── .cmd-btns (display:flex, gap:10px)
    └── .cmd-btn.glass.cmd-active (x3)
        ├── svg (28x28 아이콘, stroke-width:1.7)
        └── .cmd-btn-label (버튼 라벨)
```

### 2.3. 시각 요소 상세

| 요소 | 속성 |
|------|------|
| `.cmd-label` | `Cinzel 11px`, `letter-spacing: 0.3em`, uppercase, `color: rgba(201,169,89,0.45)` |
| `.cmd-label::before` | 30px 수평선, `linear-gradient(90deg, transparent, rgba(201,169,89,0.3))` |
| `.cmd-btn` | 96x96px, `border-radius: 18px`, `background: linear-gradient(180deg, rgba(28,28,38,0.9), rgba(18,18,26,0.9))`, `border: 1px solid var(--border)`, `box-shadow: 0 2px 10px rgba(0,0,0,0.3), inset 0 1px 0 rgba(255,255,255,0.03)` |
| `.cmd-btn:hover` | `translateY(-2px)`, `border-color: rgba(255,255,255,0.12)`, `color: var(--text-secondary)` |
| `.cmd-active` | 기능 활성화 상태 — `cursor: pointer`, 클릭 핸들러 바인딩 |
| `.cmd-btn-label` | `DM Mono 10px`, `letter-spacing: 0.1em`, uppercase |
| `.cmd-btn svg` | 28x28px, `stroke-width: 1.7` |

#### 버튼 목록

| 순서 | SVG 아이콘 | 라벨 | onclick | 기능 |
|------|------------|------|---------|------|
| 1 | user-plus (사람 + 십자) | 길드원 모집 | `openRecruitModal()` | 가챠 시스템 기반 길드원 모집 모달 오픈 |
| 2 | star (별) | 길드원 관리 | `openGuildManage()` | 길드원 목록 + 스쿼드 편성 탭 모달 오픈 |
| 3 | lock-open (자물쇠) | 해금 | `openUnlockShop()` | 해금 상점 모달 오픈 |

---

## 3. 데이터 모델

### 3.1. 전역 상태

Command Post 버튼 자체는 상태를 보유하지 않으나, 각 버튼이 참조하는 시스템 상태:

| 참조 변수 | 설명 |
|-----------|------|
| `A.gold` | 모집·해금 비용 관련 골드 잔액 |
| `A.guildLevel` | 해금 조건 판별용 길드 레벨 |
| `A.members` | 길드원 관리에서 표시할 멤버 목록 |

### 3.2. 함수 참조

| 함수 | 트리거 | 설명 |
|------|--------|------|
| `openRecruitModal()` | 길드원 모집 버튼 클릭 | 가챠 모집 모달 열기 |
| `openGuildManage()` | 길드원 관리 버튼 클릭 | 길드원 관리 모달 열기 (멤버 목록 + 스쿼드 편성 탭) |
| `openUnlockShop()` | 해금 버튼 클릭 | 해금 상점 모달 열기 |

---

## 4. 동작 규칙

### 4.1. 상태 전이

```
[Command Post 대기] ──버튼 클릭──▶ [해당 모달 오픈]
    ▲                                    │
    └────────── 모달 닫기 ───────────────┘
```

### 4.2. 핵심 로직

- **길드원 모집** (`openRecruitModal`): 가챠 시스템 모달을 열어 새 길드원을 뽑을 수 있음
- **길드원 관리** (`openGuildManage`): 현재 보유한 길드원 목록 확인 및 스쿼드 편성 탭 제공
- **해금** (`openUnlockShop`): 골드를 소비하여 잠긴 콘텐츠를 해금하는 상점 모달 오픈
- hover 시 `translateY(-2px)` 상승 애니메이션으로 인터랙티브 피드백 제공

### 4.3. 함수 매핑

| 함수 | 파일 위치 |
|------|-----------|
| `openRecruitModal()` | `guild-master-desktop.html` (inline script) |
| `openGuildManage()` | `guild-master-desktop.html` (inline script) |
| `openUnlockShop()` | `guild-master-desktop.html` (inline script) |

---

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 결과 |
|------|------|
| 버튼 hover | `translateY(-2px)` 상승 + `border-color` 하이라이트 |
| 길드원 모집 클릭 | `openRecruitModal()` — 가챠 모집 모달 오픈 |
| 길드원 관리 클릭 | `openGuildManage()` — 길드원 관리 모달 오픈 (멤버 목록 + 스쿼드 편성) |
| 해금 클릭 | `openUnlockShop()` — 해금 상점 모달 오픈 |

### 5.2. 키보드 단축키

없음.

---

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| [guild-member-recruit](../guild-member-recruit.md) | 길드원 모집 버튼 → 가챠 모집 모달 연동 |
| [guild-member](../guild-member/) | 길드원 관리 버튼 → 멤버 목록·스쿼드 편성 연동 |
| unlock-shop | 해금 버튼 → 해금 상점 모달 연동 |

---

## 업데이트 이력

| 날짜 | 내용 |
|------|------|
| 2026-04-25 | 초안 작성 (플레이스홀더 상태) |
| 2026-04-27 | 3개 버튼 전체 기능 구현 반영 — Coming Soon 제거, onclick 핸들러 및 모달 연동 문서화. 3번 버튼 "환경 설정" → "해금"으로 변경 |

---

> **최종 수정**: 2026-04-27

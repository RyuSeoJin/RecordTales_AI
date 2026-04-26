# Guild Sigil

> **문서 성격**: `Guild` 시스템의 **Guild Sigil** UI 컴포넌트 스펙.
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

- **한 줄 정의**: 화면 중앙에 위치한 길드 문장 장식 요소 (회전 링 + 텍스트 스택)
- **위치**: 중앙 (`focus-main-display`) — `.center-visual`, 화면 정중앙 absolute 배치
- **구현 상태**: ✅ 구현 완료 (정적 장식 요소)

---

## 2. UI 구조

### 2.1. 와이어프레임

```
        ┌─ .center-visual (640x640, 중앙 정렬) ─────────────┐
        │                                                     │
        │     ╭──────────── .sigil-ring ────────────╮        │
        │     │   ╭─ ::before (inset 48px) ──────╮  │        │
        │     │   │  ╭─ ::after (inset 96px) ──╮ │  │        │
        │     │   │  │                          │ │  │        │
        │     │   │  │    .sigil-core           │ │  │        │
        │     │   │  │    ┌───────────────┐     │ │  │        │
        │     │   │  │    │  .sigil-anno  │     │ │  │        │
        │     │   │  │    │ ADVENTURERS   │     │ │  │        │
        │     │   │  │    │  GUILD        │     │ │  │        │
        │     │   │  │    │               │     │ │  │        │
        │     │   │  │    │  .sigil-letter│     │ │  │        │
        │     │   │  │    │      G        │     │ │  │        │
        │     │   │  │    │               │     │ │  │        │
        │     │   │  │    │ .sigil-motto  │     │ │  │        │
        │     │   │  │    │ "Per focum,   │     │ │  │        │
        │     │   │  │    │  ad gloriam"  │     │ │  │        │
        │     │   │  │    │               │     │ │  │        │
        │     │   │  │    │ .sigil-trans  │     │ │  │        │
        │     │   │  │    │ 집중으로,     │     │ │  │        │
        │     │   │  │    │  영광으로     │     │ │  │        │
        │     │   │  │    └───────────────┘     │ │  │        │
        │     │   │  ╰─────────────────────────╯ │  │        │
        │     │   ╰───────────────────────────────╯  │        │
        │     ╰──────────────────────────────────────╯        │
        └─────────────────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.center-visual (640x640, position:absolute, top/left:50%, transform:translate(-50%,-50%), z-index:3)
├── .sigil-ring (border-radius:50%, animation:spin 120s)
│   ├── ::before (inset:48px, border:1px dashed)
│   └── ::after (inset:96px, border:1px solid)
└── .sigil-core (z-index:2, flex-direction:column, align-items:center)
    ├── .sigil-anno
    ├── .sigil-letter
    ├── .sigil-motto
    └── .sigil-trans
```

### 2.3. 시각 요소 상세

#### 회전 링 (.sigil-ring)

| 레이어 | 속성 |
|--------|------|
| `.sigil-ring` | `border: 1px solid rgba(201,169,89,0.1)`, `border-radius: 50%`, `animation: spin 120s linear infinite` |
| `::before` | `inset: 48px`, `border: 1px dashed rgba(201,169,89,0.1)` |
| `::after` | `inset: 96px`, `border: 1px solid rgba(201,169,89,0.07)` |

- 3중 동심원 구조, 가장 바깥 링이 120초에 1회전하는 느린 회전 애니메이션
- pseudo-element들은 부모와 함께 회전

#### 텍스트 스택 (.sigil-core)

| 요소 | 폰트 | 크기 | 색상 | 내용 |
|------|-------|------|------|------|
| `.sigil-anno` | Cinzel | 11px | `rgba(201,169,89,0.4)` | `— Anno Magii MMXXVI —` |
| `.sigil-letter` | Cinzel bold | 80px | `var(--gold-soft)` | `G` |
| `.sigil-motto` | DM Serif Display italic | 17px | `var(--text-secondary)` | `"Per focum, ad gloriam"` |
| `.sigil-trans` | DM Mono | 10px | `var(--text-muted)` | `집중으로, 영광으로` |

#### 추가 시각 효과

| 효과 | 설명 |
|------|------|
| `.sigil-anno` | `letter-spacing: 0.5em`, uppercase |
| `.sigil-letter` | `text-shadow: 0 0 40px rgba(232,200,124,0.2)` — 금빛 글로우 |
| `.sigil-motto` | `letter-spacing: 0.06em`, `margin-top: 14px` |
| `.sigil-trans` | `letter-spacing: 0.28em`, uppercase, `margin-top: 20px` |
| `.center-visual` | `pointer-events: none` — 클릭 이벤트 통과 |

---

## 3. 데이터 모델

### 3.1. 전역 상태

없음 — 순수 정적 장식 요소.

### 3.2. 데이터 스키마

해당 없음. 모든 텍스트가 HTML에 하드코딩.

#### 하드코딩 텍스트

| 위치 | 텍스트 | 언어 |
|------|--------|------|
| `.sigil-anno` | `— Anno Magii MMXXVI —` | 라틴어 스타일 (마법의 해 2026) |
| `.sigil-letter` | `G` | 영문 (Guild 약자) |
| `.sigil-motto` | `"Per focum, ad gloriam"` | 라틴어 (집중을 통해, 영광으로) |
| `.sigil-trans` | `집중으로, 영광으로` | 한국어 번역 |

---

## 4. 동작 규칙

### 4.1. 상태 전이

상태 전이 없음 — 페이지 로드 즉시 표시, 상태 변화 없음.

### 4.2. 핵심 로직

- CSS `@keyframes spin` 애니메이션만으로 동작 (JS 개입 없음)
- 120초 1회전 = 3초에 1도씩 회전하는 극도로 느린 애니메이션
- `pointer-events: none`으로 중앙 영역이 마우스 이벤트를 차단하지 않음

### 4.3. 함수 매핑

관련 JavaScript 함수 없음.

---

## 5. 사용자 상호작용

### 5.1. 조작 방법

없음 — `pointer-events: none` 설정으로 모든 마우스 이벤트가 하위 요소로 통과.

### 5.2. 키보드 단축키

없음.

---

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| Stage / Particles | 동일 `.stage` 컨테이너 내 배경 레이어와 공존 |
| Focus Main Display | 집중 모드 진입 시 동일 중앙 영역에 타이머/스톱워치 UI 오버레이 |

---

> **최종 수정**: 2026-04-25

# Clock Display

> **문서 성격**: `글로벌 UI`의 **시계** 컴포넌트 스펙.
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

- **한 줄 정의**: 현재 시간과 날짜를 실시간으로 표시하는 정적 시계 컴포넌트
- **위치**: 우측 상단 `.tr-block` 내부, `.clock-plate` 영역
- **구현 상태**: ✅ 구현 완료

## 2. UI 구조

### 2.1. 와이어프레임

```
┌─── .tr-block (absolute, top:28px, right:36px) ────┐
│                                                     │
│   ┌─── .clock-plate ────────────────┐              │
│   │  .clock-time   "14:32"          │  ← 우측 정렬 │
│   │  .clock-date   "2026.04.25·FRI" │              │
│   └─────────────────────────────────┘              │
│                                                     │
│   ┌─── .tr-bottom-row ─────────────┐              │
│   │  [session-mini] [nav-bar]       │              │
│   └─────────────────────────────────┘              │
└─────────────────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.tr-block                    ← 우측 상단 컨테이너 (absolute, z-index:60)
├── .clock-plate             ← 시계 래퍼 (flex-column, 우측 정렬)
│   ├── .clock-time          ← 시간 표시 (HH:MM)
│   └── .clock-date          ← 날짜 표시 (YYYY.MM.DD · DAY)
└── .tr-bottom-row           ← 하단 행 (session-mini + nav-bar)
```

### 2.3. 시각 요소 상세

| 요소 | 속성 | 값 |
|------|------|----|
| `.clock-plate` | 정렬 | `flex-direction: column`, `align-items: flex-end`, `gap: 2px` |
| `.clock-time` | 폰트 | `DM Mono`, `28px`, `font-weight: 300` |
| `.clock-time` | 색상 | `var(--text-primary)` |
| `.clock-time` | 자간 | `letter-spacing: 0.04em`, `line-height: 1` |
| `.clock-date` | 폰트 | `DM Mono`, `10px`, `uppercase` |
| `.clock-date` | 색상 | `var(--text-muted)` |
| `.clock-date` | 자간 | `letter-spacing: 0.2em` |

## 3. 데이터 모델

### 3.1. 전역 상태

해당 없음. 시계는 전역 상태(`A`)를 사용하지 않는다.

### 3.2. 데이터 스키마

해당 없음. 시계는 `Date` 객체에서 직접 값을 읽어 표시한다.

## 4. 동작 규칙

### 4.1. 상태 전이

상태 전이 없음. 단일 상태로 동작한다.

### 4.2. 핵심 로직

- 페이지 로드 시 IIFE + 재귀 `setTimeout(tick, 15000)`으로 **15초마다** 시간·날짜를 갱신한다.
- 시간 포맷: `HH:MM` (24시간제)
- 날짜 포맷: `YYYY.MM.DD · DAY` (요일은 영문 3자리 대문자, 예: `THU`)

### 4.3. 함수 매핑

| 함수 | 역할 |
|------|------|
| `tick()` (IIFE) | `(function tick(){ ... setTimeout(tick, 15000); })()` — 15초 간격 재귀 호출로 `#clockTime`, `#clockDate` 요소의 `textContent`를 갱신 |

**DOM 요소 ID**:
- `#clockTime` — 시간 텍스트
- `#clockDate` — 날짜 텍스트

## 5. 사용자 상호작용

### 5.1. 조작 방법

없음. 정적 표시 전용 컴포넌트로 사용자 입력을 받지 않는다.

### 5.2. 키보드 단축키

해당 없음.

### 5.3. 이벤트 흐름

```
페이지 로드 → tick() IIFE 즉시 실행 → clockTime.textContent 갱신
                                    → clockDate.textContent 갱신
                                    → setTimeout(tick, 15000) 재귀 예약
```

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| `navigation-bar` | 같은 `.tr-block` 내에 함께 배치 |
| `panel-system` | 패널 열림/닫힘과 무관하게 항상 표시 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-25 | 초기 작성 |

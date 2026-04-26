# Navigation Bar — 내비게이션 바

> **문서 성격**: 우상단 `schedule-nav-bar`의 **내비게이션 바**(`.nav-bar`) 시스템 스펙.
> Focus / Quests / Archive 3개 모드 진입 버튼과 조건부 UI(session-mini, nav-dot)를 담당합니다.
> 용어 정의는 `project-dictionary.md` 2.2.2.2., 시각 토큰은 `html-design-style-guide.md`를 따릅니다.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 목차

1. [현재 상태](#1-현재-상태)
2. [변경 목표 (이번 작업 범위)](#2-변경-목표-이번-작업-범위)
3. [UI 구조](#3-ui-구조)
4. [데이터 모델](#4-데이터-모델)
5. [동작 규칙](#5-동작-규칙)
6. [상호작용](#6-상호작용)
7. [다이어그램](#7-다이어그램)
8. [구현 체크리스트](#8-구현-체크리스트)
9. [관련 시스템](#9-관련-시스템)

---

## 1. 현재 상태

- 구현 정도: 📋계획됨
- 코드 위치: `guild-master-desktop.html` — `.tr-block > .nav-bar`

## 2. 변경 목표 (이번 작업 범위)

## 3. UI 구조

하위 명칭 (공통): `nav-btn`, `nav-btn-label`

### 3.1. Focus 버튼

- 식별색: 코랄 계열 (`--focus-c` = `#e8a87c`)
- 연동 UI: `session-mini` (Focus 세션 조건부 확장)
  - 하위 명칭: `sm-indicator`, `sm-body`, `sm-time`, `sm-phase`, `sm-dots`, `sm-dot`

### 3.2. Quests 버튼

- 식별색: 골드 계열 (`--todo-c`)
- 연동 UI: `nav-dot` (지연 알림 도트)

### 3.3. Archive 버튼

- 식별색: 라벤더 계열 (`--diary-c`)
- 연동 UI: 없음

## 4. 데이터 모델

## 5. 동작 규칙

## 6. 상호작용

## 7. 다이어그램

## 8. 구현 체크리스트

- [ ] ...

## 9. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `project-dictionary.md` 2.2.2.2. | nav-bar 용어 정의 |
| `html-design-style-guide.md` | 시각 토큰, 색상, 타이포그래피 |
| `clock.md` | 같은 섹션(schedule-nav-bar)의 시계 플레이트 |
| `focus-panel.md` | Focus 모드 상세 (session-mini 포함) |
| `quests-panel.md` | Quests 모드 상세 (nav-dot 표시 조건) |
| `archive-panel.md` | Archive 모드 상세 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 |

# Focus Panel — 집중 패널

> **문서 성격**: `detail-side-panel`의 **Focus 모드** 시스템 스펙.
> 집중 기능(타이머·스톱워치)을 제공하며, session-mini 위젯도 이 시스템에서 관리합니다.
> 용어 정의는 `project-dictionary.md` 2.2.6.4., 시각 토큰은 `html-design-style-guide.md`를 따릅니다.
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
- 코드 위치: `guild-master-desktop.html` — `.side-panel` (Focus 모드 활성 시)

## 2. 변경 목표 (이번 작업 범위)

## 3. UI 구조

### 3.1. 모드 전환

- 모드 스위처: `mode-sw-ind`, `mode-btn.timer`, `mode-btn.sw`

### 3.2. 타이머 모드

- 설정 필드: 집중 시간, 휴식 시간, 루틴 모드, 반복 횟수, 자동 재시작
- 타이머 디스플레이 링: `timer-viz`, `ring-arc`, `ring-time`, `ring-phase`, `cycle-dots`, `ring-ctrls`

### 3.3. 스톱워치 모드

- 스톱워치 디스플레이 링: `sw-viz`, `swRingArc`, `swDisp`, `sw-ctrls`
- 랩 기록: `swLaps`

### 3.4. session-mini (nav-bar 조건부 확장)

- 하위 명칭: `sm-indicator`, `sm-body`, `sm-time`, `sm-phase`, `sm-dots`, `sm-dot`

## 4. 데이터 모델

## 5. 동작 규칙

## 6. 상호작용

## 7. 다이어그램

## 8. 구현 체크리스트

- [ ] ...

## 9. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `project-dictionary.md` 2.2.6.4. | Focus 모드 용어 정의 |
| `project-dictionary.md` 2.2.2.2.1. | session-mini 용어 정의 |
| `html-design-style-guide.md` | 시각 토큰, 색상, 타이포그래피 |
| `navigation-bar.md` | Focus 버튼 (진입 경로) |
| `record-modal.md` | Focus 세션 종료 시 기록 모달 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 |

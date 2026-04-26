# Archive Panel — 아카이브 패널

> **문서 성격**: `detail-side-panel`의 **Archive 모드** 시스템 스펙.
> 완료된 퀘스트와 Focus 기록을 캘린더 기반으로 조회합니다.
> 용어 정의는 `project-dictionary.md` 2.2.6.6., 시각 토큰은 `html-design-style-guide.md`를 따릅니다.
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
- 코드 위치: `guild-master-desktop.html` — `.side-panel` (Archive 모드 활성 시)

## 2. 변경 목표 (이번 작업 범위)

## 3. UI 구조

### 3.1. 좌측 — 캘린더

- `cal-wrap`, `cal-hdr`, `cal-title`, `cal-nav`
- `cal-wds` (요일 라벨), `calGrid` (날짜 셀)
- 날짜 셀 내부: `day-num`, `day-dots` (f/s/t 인디케이터), `day-chk`

### 3.2. 우측 — 아카이브 데이터

- 기록 필터: 전체 / Focus / Stopwatch
- 선택일 스트립: `day-strip-label`, `day-strip-close`
- 집중 기록: `record-item`, `rec-top`, `rec-badges`, `rec-clock`, `rec-time`, `rec-notes`
- 할 일: `arch-todo-item`, `arch-todo-chk-disp`, `arch-todo-title`, `arch-todo-cat`

## 4. 데이터 모델

## 5. 동작 규칙

## 6. 상호작용

## 7. 다이어그램

## 8. 구현 체크리스트

- [ ] ...

## 9. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `project-dictionary.md` 2.2.6.6. | Archive 모드 용어 정의 |
| `html-design-style-guide.md` | 시각 토큰, 색상, 타이포그래피 |
| `navigation-bar.md` | Archive 버튼 (진입 경로) |
| `quests-panel.md` | 퀘스트 데이터 연동 |
| `focus-panel.md` | Focus 기록 데이터 연동 |
| `record-modal.md` | 기록 저장 모달 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 |

# Record Modal — 기록 저장 모달

> **문서 성격**: 전역 트리거 기반 UI인 **기록 저장 모달**(`.rec-modal-wrap`) 시스템 스펙.
> Focus 세션 완료·종료 시 화면 중앙에 나타나는 기록 저장용 모달입니다.
> 용어 정의는 `project-dictionary.md` 2.3.1., 시각 토큰은 `html-design-style-guide.md`를 따릅니다.
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
- 코드 위치: `guild-master-desktop.html` — `.rec-modal-wrap`

## 2. 변경 목표 (이번 작업 범위)

## 3. UI 구조

트리거: Focus 타이머 사이클 완료 / 타이머 중도 종료 / 스톱워치 종료

하위 명칭:
- 컨테이너: `rec-modal-wrap`, `rec-modal`
- 헤더: `rm-icon`, `rm-title`, `rm-sub`, `rm-stat`, `rm-stat-label`, `rm-stat-val`
- 카테고리 섹션: `sec-label`, `cat-chips-wrap`, `cat-chip`, `cat-chip-del`, `cat-add-row`, `cat-add-input`, `cat-add-btn`
- 메모: `form-textarea`
- 액션: `rm-actions`, `btn-rm-skip`, `btn-rm-save`

## 4. 데이터 모델

## 5. 동작 규칙

## 6. 상호작용

## 7. 다이어그램

## 8. 구현 체크리스트

- [ ] ...

## 9. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `project-dictionary.md` 2.3.1. | record-modal 용어 정의 |
| `html-design-style-guide.md` | 시각 토큰, 색상, 타이포그래피 |
| `focus-panel.md` | Focus 세션 (트리거 원천) |
| `archive-panel.md` | 저장된 기록 조회 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 |

# Ambient Sound — 환경음 믹서

> **문서 성격**: 좌하단 `audio-player-panel`의 **환경음 믹서**(`.ambient-btn` + `.ambient-overlay`) 시스템 스펙.
> 환경음(빗소리·바람·불·숲·바다) 믹서를 제공합니다. 세계관 명칭: "Ambient Soundscape".
> 용어 정의는 `project-dictionary.md` 2.2.3.2., 시각 토큰은 `html-design-style-guide.md`를 따릅니다.
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
- 코드 위치: `guild-master-desktop.html` — `.bl-block > .ambient-btn`, `.ambient-overlay`

## 2. 변경 목표 (이번 작업 범위)

## 3. UI 구조

하위 명칭:
- 버튼: `ambient-btn`
- 오버레이 (조건부): `ambient-overlay`, `ambient-inner`, `ambient-title`
- 개별 사운드 행: `amb-row`, `amb-icon`, `amb-name`, `amb-track`, `amb-fill`, `amb-val`

## 4. 데이터 모델

## 5. 동작 규칙

## 6. 상호작용

## 7. 다이어그램

## 8. 구현 체크리스트

- [ ] ...

## 9. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `project-dictionary.md` 2.2.3.2. | ambient-btn + ambient-overlay 용어 정의 |
| `html-design-style-guide.md` | 시각 토큰, 색상, 타이포그래피 |
| `music-player.md` | 같은 섹션(audio-player-panel)의 배경 음악 플레이어 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 |

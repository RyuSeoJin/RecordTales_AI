# Music Player — 배경 음악 플레이어

> **문서 성격**: 좌하단 `audio-player-panel`의 **배경 음악 플레이어**(`.music-player`) 시스템 스펙.
> 사전 정의된 배경 음악 트랙을 재생합니다. 세계관 명칭: "Bard's Refrain".
> 용어 정의는 `project-dictionary.md` 2.2.3.1., 시각 토큰은 `html-design-style-guide.md`를 따릅니다.
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
- 코드 위치: `guild-master-desktop.html` — `.bl-block > .music-player`

## 2. 변경 목표 (이번 작업 범위)

## 3. UI 구조

하위 명칭:
- 헤더: `pl-hdr`, `pl-ornament`, `pl-label`, `pl-list-btn`
- 트랙 리스트 (조건부): `player-tracks`, `tl-hdr-label`, `tl-item`, `tl-icon`, `tl-body`, `tl-track-title`, `tl-track-sub`, `tl-dur`
- 현재 곡: `pl-track-row`, `pl-art`, `pl-art-glyph`, `pl-meta`, `pl-title`, `pl-sub`, `eq-bars`, `eq-bar`
- 진행 바: `pl-progress`, `p-time`, `p-bar`, `p-fill`
- 컨트롤: `pl-controls`, `pb`, `pb.play`, `vol-row`, `vol-icon`, `vol-track`, `vol-fill`

## 4. 데이터 모델

## 5. 동작 규칙

## 6. 상호작용

## 7. 다이어그램

## 8. 구현 체크리스트

- [ ] ...

## 9. 관련 시스템

| 문서 | 다루는 것 |
|------|---------|
| `project-dictionary.md` 2.2.3.1. | music-player 용어 정의 |
| `html-design-style-guide.md` | 시각 토큰, 색상, 타이포그래피 |
| `ambient-sound.md` | 같은 섹션(audio-player-panel)의 환경음 믹서 |

---

## 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-24 | 초안 작성 |

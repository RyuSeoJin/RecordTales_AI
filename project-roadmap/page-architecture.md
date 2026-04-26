# RecordTales — Page Architecture

> **문서 성격**: HTML 프로토타입의 **구조·동작 인프라**를 정의합니다.
> 페이지 레이아웃 좌표, z-index 계층, 초기화 시퀀스, 글로벌 함수·이벤트 핸들러 등
> **"어떻게 조립되는지"**를 다룹니다.
> 시각 디자인 토큰·스타일 규칙(색상, 폰트, 모션, 컴포넌트)은 `html-design-style-guide.md` 참조.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 📑 목차

1. [개요](#1-개요)
2. [레이아웃 좌표계](#2-레이아웃-좌표계)
3. [Z-index 계층 구조](#3-z-index-계층-구조)
4. [초기화 시퀀스](#4-초기화-시퀀스)
5. [폰트 로딩](#5-폰트-로딩)
6. [글로벌 유틸리티 함수](#6-글로벌-유틸리티-함수)
7. [글로벌 이벤트 핸들러](#7-글로벌-이벤트-핸들러)
8. [관련 문서](#8-관련-문서)

---

## 1. 개요

- **해상도**: 1920×1080px 고정
- **스케일링**: `Math.min(innerWidth/1920, innerHeight/1080)` 비율로 `transform: scale()` 적용, `transform-origin: center center`
- **구조**: 단일 HTML 파일, `<style>` + `<body>` + `<script>` 인라인
- **컨테이너**: `.stage-wrap` (viewport 가득 채움, flex 중앙 정렬) → `.stage` (1920×1080, `position: relative`, `overflow: hidden`)

---

## 2. 레이아웃 좌표계

`.stage` 내부의 모든 블록은 `position: absolute`로 배치된다.

### 2.1. 전체 맵

```
+── .stage (1920×1080, position:relative) ──────────────────────────+
│                                                                    │
│  .stage::before (vignette, z:1, inset:0)                          │
│  .particles (z:2, inset:0, 22개 .particle)                        │
│  .center-visual (z:3, 640×640, top/left:50% translate:-50%)       │
│    └── .sigil-ring + .sigil-core                                  │
│                                                                    │
│  ┌─ .tl-block ────────────┐        ┌─────────── .tr-block ──┐    │
│  │ top:28px  left:36px    │        │   top:28px  right:36px  │    │
│  │ z:20   w:296px         │        │   z:60   align:flex-end │    │
│  │ flex-col  gap:10px     │        │   flex-col  gap:16px    │    │
│  │                        │        │                         │    │
│  │ [.guild-plate]         │        │ [.clock-plate]          │    │
│  │ [.todo-overlay]        │        │ [.tr-bottom-row]        │    │
│  └────────────────────────┘        │  [session-mini][nav-bar]│    │
│                                    └─────────────────────────┘    │
│                                                                    │
│                              ┌─── .side-panel ──────────┐         │
│                              │ top:204px  right:36px     │         │
│                              │ z:50   w:460px (760 wide) │         │
│                              │ max-height:870px          │         │
│                              └───────────────────────────┘         │
│                                                                    │
│  ┌─ .bl-block ────────────┐        ┌────── .br-block ───────┐    │
│  │ bottom:28px left:36px  │        │ bottom:28px right:36px  │    │
│  │ z:20   w:356px         │        │ z:20   align:flex-end   │    │
│  │ flex-col  gap:8px      │        │ flex-col  gap:12px      │    │
│  │                        │        │                         │    │
│  │ [.ambient-overlay]     │        │ [.cmd-label]            │    │
│  │ [.ambient-btn]         │        │ [.cmd-btns (3 buttons)] │    │
│  │ [.music-player]        │        │                         │    │
│  └────────────────────────┘        └─────────────────────────┘    │
│                                                                    │
│  .panel-backdrop (z:49, inset:0)                                  │
│  .rec-modal-wrap (z:200, inset:0, flex 중앙 정렬)                 │
���  .cat-add-modal-wrap (z:250, inset:0, flex 중앙 정렬)             │
│  .toast (z:300, bottom:28px, left:50%, translateX:-50%)           │
+────────────────────────────────────────────────────────────────────+
```

### 2.2. 블록별 좌표 요약

| 블록 | position | top/bottom | left/right | width | z-index | 내부 배치 |
|------|----------|------------|------------|-------|---------|----------|
| `.tl-block` | absolute | top: 28px | left: 36px | 296px | 20 | flex-column, gap: 10px |
| `.tr-block` | absolute | top: 28px | right: 36px | auto | 60 | flex-column, align: flex-end, gap: 16px |
| `.bl-block` | absolute | bottom: 28px | left: 36px | 356px | 20 | flex-column, gap: 8px |
| `.br-block` | absolute | bottom: 28px | right: 36px | auto | 20 | flex-column, align: flex-end, gap: 12px |
| `.center-visual` | absolute | top/left: 50%, translate: -50% | — | 640×640px | 3 | flex 중앙 정렬 |
| `.side-panel` | absolute | top: 204px | right: 36px | 460px / 760px (`.wide`) | 50 | flex-column, max-height: 870px |

### 2.3. 오버레이 레이어

| 요소 | position | 범위 | 정렬 | 비고 |
|------|----------|------|------|------|
| `.panel-backdrop` | absolute | inset: 0 | — | 클릭 시 패널 닫기. opacity 트랜지션 |
| `.rec-modal-wrap` | absolute | inset: 0 | flex 중앙 | background: rgba(0,0,0,0.7), blur(10px) |
| `.cat-add-modal-wrap` | absolute | inset: 0 | flex 중앙 | background: rgba(0,0,0,0.75), blur(10px) |
| `.toast` | absolute | bottom: 28px, left: 50% | translateX(-50%) | 2500ms 후 자동 사라짐 |

---

## 3. Z-index 계층 구조

낮은 번호가 아래, 높은 번호가 위.

| z-index | 레이어 | 요소 | 이유 |
|---------|--------|------|------|
| 1 | 배경 비네트 | `.stage::before` | 가장자리 어둡게 처리, 상호작용 없음 |
| 2 | 파티클 | `.particles` | 배경 장식, 상호작용 없음 |
| 3 | 중앙 시길 | `.center-visual` | 배경 장식, 상호작용 없음 |
| 20 | 코너 블록 | `.tl-block`, `.bl-block`, `.br-block` | 항상 보이는 고정 UI |
| 49 | 패널 백드롭 | `.panel-backdrop` | 패널 뒤 클릭 캐치 영역 |
| 50 | 사이드 패널 | `.side-panel` | 백드롭 바로 위 |
| 60 | 우상단 블록 | `.tr-block` | 패널이 열려도 시계·네비게이션 클릭 가능해야 하므로 패널(50)보다 높음 |
| 200 | 기록 모달 | `.rec-modal-wrap` | 모든 패널 위에 표시 |
| 250 | 카테고리 추가 모달 | `.cat-add-modal-wrap` | 기록 모달 위에 중첩 가능 |
| 300 | 토스트 | `.toast` | 최상위 — 어떤 상태에서도 보여야 함 |

---

## 4. 초기화 시퀀스

페이지 로드 시 `<script>` 블록이 순서대로 실행된다.

### 4.1. IIFE (즉시 실행 함수)

```
1. Stage Scale IIFE
   - .stage에 scale 적용
   - window.resize 이벤트 리스너 등록

2. Particles IIFE
   - #particles에 22개 .particle div 동적 생성
   - 각 particle: left/top 랜덤(0~100%), animation-delay 랜���(-14~0s), duration 10~20s

3. Clock IIFE (tick)
   - clockTime: HH:MM 형식, clockDate: YYYY.MM.DD · DAY 형식
   - setTimeout(tick, 15000) 재귀 — 15초 간격

4. Ambient Sounds IIFE
   - SOUNDS 배열(5개)로 #ambientSounds에 .amb-row HTML 생성
```

### 4.2. 전역 상태 선언

```
5. const A = { ... }     ← 전역 앱 상태 (~60개 속성)
6. const CIRC_P = 465    ← 2π×74 (SVG 원 둘레)
```

### 4.3. 함수 정의

```
7. 유틸리티 함수 ($, fmt, esc, dateKey 등)
8. 시스템별 함수 (panel, focus, quests, archive, audio)
```

### 4.4. 이벤트 리스너 등록

```
9. document.keydown → Escape: closePanel() + closeRecModal()
10. document.click (패널) → 외부 클릭 시 패널 닫기
11. document.click (드롭다운) → 음악 재생목록/앰비언트 외부 클릭 닫기
```

### 4.5. 초기 렌더링 호출

```
12. renderTrackInfo()      ← 현재 트랙 정보 표시
13. renderTrackList()      ← 재생목록 HTML 생성
14. renderTlTodos()        ← 좌상단 오늘의 퀘스트 목록
15. initMidnightRollover() ← 자정 롤오버 스케줄 등록 + visibilitychange 리스너
```

---

## 5. 폰트 로딩

### 5.1. CDN 링크

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;500;600;700&family=DM+Serif+Display:ital@0;1&family=DM+Mono:wght@300;400;500&family=Noto+Sans+KR:wght@300;400;500&display=swap" rel="stylesheet">
```

### 5.2. 폰트 패밀리별 용도

| 폰트 | weight | 용도 |
|-------|--------|------|
| `Cinzel` | 400, 500, 600, 700 | 제목, 라벨, 장식 텍스트 (길드명, 섹션 라벨 등) |
| `DM Serif Display` | normal, italic | 시길 모토, 대형 장식 텍스트 |
| `DM Mono` | 300, 400, 500 | 시간, 숫자, 메타 라벨, 버튼 텍스트 |
| `Noto Sans KR` | 300, 400, 500 | 한글 본문, 설명, 폼 입력 |

> 각 폰트의 구체적 적용 위치와 font-size 계층은 `html-design-style-guide.md` §3 참조.

---

## 6. 글로벌 유틸리티 함수

어떤 시스템에도 속하지 않는 공용 함수들.

### 6.1. DOM / 포맷

| 함수 | 시그니처 | 역할 |
|------|----------|------|
| `$` | `$(id) → Element` | `document.getElementById` 단축 |
| `fmt` | `fmt(seconds) → string` | 초를 `HH:MM:SS` 또는 `MM:SS`로 변환. 시간이 0이면 `MM:SS` |
| `fmtT` | `fmtT(seconds) → string` | 초를 `M:SS`로 변환 (음악 전용, 시간 단위 없음) |
| `esc` | `esc(string) → string` | HTML 이스케이프 (`&`, `<`, `>`) |

### 6.2. 날짜 조작

| 함수 | 시그니처 | 역할 |
|------|----------|------|
| `dateKey` | `dateKey(Date) → string` | Date를 `"YYYY.MM.DD"` 문자열로 변환 |
| `dateLong` | `dateLong(Date) → string` | Date를 `"2026년 4월 23일 (수)"` 한글 형식으로 변환 |
| `parseKey` | `parseKey(dk) → Date` | `"YYYY.MM.DD"` 문자열을 Date로 역변환 |
| `todayKey` | `todayKey() → string` | 오늘의 dateKey 반환 |
| `dkToCompact` | `dkToCompact(dk) → string` | `"2026.04.23"` → `"20260423"` (마침표 제거, ID 생성용) |
| `addDays` | `addDays(dk, n) → string` | dateKey에 n일 가감 |
| `addYears` | `addYears(dk, n) → string` | dateKey에 n년 가감 |
| `weekdayName` | `weekdayName(dk) → string` | dateKey를 `"수요일"` 한글 요일로 변환 |

### 6.3. 표시 헬퍼

| 함수 | 시그니처 | 역할 |
|------|----------|------|
| `recLabel` | `recLabel(recType) → string` | 반복 타입 → 한글 라벨 (`none→"없음"`, `daily→"매일"`, `weekly→"매주"`, `monthly→"매달"`) |

### 6.4. 글로벌 효과

| 함수 | 시그니처 | 역할 |
|------|----------|------|
| `showToast` | `showToast(msg)` | `.toast`에 메시지 표시 → `.show` 추가 → 2500ms 후 제거 |
| `playAlarm` | `playAlarm()` | Web Audio API로 880Hz 경고음 3회 재생 (200ms 간격, 0.4초 디케이) |

---

## 7. 글로벌 이벤트 핸들러

`document` 레벨에 등록되는 3개의 이벤트 리스너.

### 7.1. Escape 키

```
document.keydown → e.key === 'Escape'
  → closePanel() + closeRecModal() 동시 호출
```

패널과 기록 모달을 한 번에 닫는다.

### 7.2. 외부 클릭 — 패널 닫기

```
document.click
  1. DOM 제거 체크: !document.contains(e.target) → return
     (innerHTML 재렌더로 클릭 대상이 DOM에서 제거된 경우 무시)
  2. e.target이 #sidePanel 내부 → 무시
  3. e.target이 .tr-block 내부 → 무시 (네비게이션 영역)
  4. A.currentPanel이 있으면 → closePanel()
```

**엣지케이스**: 패널 내부에서 버튼 클릭 시 `innerHTML` 재렌더가 발생하면, 클릭 이벤트가 버블링될 때 원래 타겟이 DOM에서 이미 제거되어 있다. `!document.contains(e.target)` 체크로 이 경우 패널이 닫히지 않도록 방지한다.

### 7.3. 외부 클릭 — 드롭다운 닫기

```
document.click
  - #musicPlayer 외부 클릭 → playerTracks.classList.remove('open')
  - #ambientBtn 및 #ambientOverlay 외부 클릭 → ambOpen=false, overlay/btn 비활성화
```

### 7.4. 탭 복귀 — 자정 롤오버

```
document.visibilitychange
  → !document.hidden일 때 processRollover() 호출
  (탭이 백그라운드에 있다가 돌아왔을 때, 자정을 넘겼는지 체크)
```

`initMidnightRollover()`에서 등록하며, 최초 로드 시 `scheduleMidnightRollover()`로 자정 타이머도 예약한다.

---

## 8. 관련 문서

| 문서 | 다루는 것 |
|------|---------|
| `html-design-style-guide.md` | 시각 디자인 토큰, 스타일 규칙 (색상, 폰트, 모션, 컴포넌트) |
| `project-identity.md` | 프로젝트 철학, 세계관, UX 원칙 |
| `project-dictionary.md` | UI 명칭, 레이아웃 영역 용어 |
| `project-wiki/` | 시스템별 상세 스펙 (폴더 구성은 `project-wiki-folder-guide.md` 참조) |

---

> **최종 수정**: 2026-04-25

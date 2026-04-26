# RecordTales — HTML Design Style Guide

> **문서 성격**: **HTML 프로토타입 전용** 시각 디자인 기준 + 작업 제약.
> 색상·폰트·간격·모션·컴포넌트 등 **시각 디자인 토큰과 스타일 규칙**을 정의합니다.
> 페이지 레이아웃 좌표, z-index 계층, 초기화 시퀀스, 글로벌 함수 등 **구조·동작 인프라**는 `page-architecture.md` 참조.
> 개발 최종은 언리얼 엔진이지만, 이 문서가 다루는 내용은 **HTML 프로토타입 단계**에 국한됩니다.
> 언리얼 전환 시 이 문서의 시각 언어(색상 계열·타이포 위계·모션 철학)는 이식되지만, 구체적 CSS 값·기술 제약은 재번역 대상입니다.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 📑 목차

- [0. 이 문서를 작성·수정하기 전에 반드시 읽을 것](#0-이-문서를-작성수정하기-전에-반드시-읽을-것)
  - [0.1. 문서 범위 (Scope)](#01-문서-범위-scope)
  - [0.2. 토큰 추가·수정 원칙](#02-토큰-추가수정-원칙)
  - [0.3. 언리얼 전환 관점](#03-언리얼-전환-관점)
- [1. HTML 프로토타입 작업 제약](#1-html-프로토타입-작업-제약)
- [2. 색상 (Colors)](#2-색상-colors)
- [3. 타이포그래피 (Typography)](#3-타이포그래피-typography)
- [4. 모서리·간격 (Corners & Spacing)](#4-모서리간격-corners--spacing)
- [5. 모션 (Motion)](#5-모션-motion)
- [6. 표면 효과 (Surface Effects)](#6-표면-효과-surface-effects)
- [7. 아이콘 (Iconography)](#7-아이콘-iconography)
- [8. 공용 UI 컴포넌트](#8-공용-ui-컴포넌트)
- [9. 전역 UI 요소](#9-전역-ui-요소)
- [10. 관련 문서](#10-관련-문서)

---

## 0. 이 문서를 작성·수정하기 전에 반드시 읽을 것

> **AI 에이전트 및 작성자는 이 섹션을 먼저 읽고 내용을 숙지한 뒤에만 이후 섹션을 수정한다.**

### 0.1. 문서 범위 (Scope)

**이 문서에 들어가는 것**:

- HTML 프로토타입의 **작업 제약** (해상도·파일 구조·외부 의존성 등)
- CSS 디자인 토큰 (색·모서리·트랜지션)
- 타이포그래피 위계·용도 매핑
- 모션 규칙과 애니메이션 패턴
- 글래스 모피즘·배경 그라데이션 등 공통 표면 효과
- 아이콘의 공통 스타일 규칙
- 전역 UI 요소 (toast 등)

**이 문서에 들어가지 않는 것**:

- 페이지 레이아웃 좌표·z-index·초기화 시퀀스·글로벌 함수 → `page-architecture.md`
- 개별 시스템의 UI 구현 세부 → `project-wiki/` 내 각 시스템 문서
- UI 요소의 명칭·구조 정의 → `project-dictionary.md`
- 프로젝트 철학·정체성 → `project-identity.md`
- 데이터 스키마 → `project-data.md` (예정)
- **언리얼 엔진 최종본의 시각 가이드** → (향후 별도 문서로 작성)


### 0.2. 토큰 추가·수정 원칙

1. **재사용 우선**: 새 값이 필요할 때 기존 토큰으로 표현 가능한지 먼저 확인
2. **명명 규칙**:
   - kebab-case (`--focus-c`, `--border-gold`)
   - 식별색: `-c` 접미사 (`--focus-c`, `--todo-c`)
   - 투명도 약화 변형: `-dim` 접미사 (`--focus-dim`)
   - 테두리 변형: `-b` 접미사 (`--glass-b`)
3. **인라인 값 지양**: CSS에서 직접 hex를 쓰지 않고 `var(--token)`으로 참조
4. **카테고리 준수**: 새 토큰은 이 문서의 해당 섹션(색·간격·모션 등)에 등록하고 사용처를 명시

### 0.3. 언리얼 전환 관점

이 문서는 **현재 HTML 프로토타입 기준**으로 작성되어 있습니다.

- **그대로 이식**: 디자인 철학, 색상 계열·팔레트, 타이포 위계, 모션 원칙
- **재번역 필요**: 구체적 CSS 값·토큰 명칭, `backdrop-filter`·`box-shadow` 같은 CSS 고유 속성은 언리얼 UMG/Slate 스타일 시스템으로 재매핑

즉 **시각 언어의 뼈대는 불변, CSS 구현 디테일은 교체** 관점으로 문서를 읽으세요.

---

## 1. HTML 프로토타입 작업 제약

HTML 프로토타입(`guild-master-desktop.html`)을 유지·개선할 때 **반드시** 지키는 규칙입니다. 이 제약은 **HTML 단계에만 적용**되며, 언리얼 전환 후에는 해제됩니다.

| 제약 | 실제 반영 |
|------|----------|
| **1920×1080 고정** | 반응형 처리 불필요. 고정 좌표 기반 레이아웃. `stage-wrap` 컨테이너가 뷰포트 크기에 맞춰 전체 스케일 |
| **단일 HTML 파일** | 모든 HTML·CSS·JS를 한 파일에. 번들러·프레임워크·패키지 매니저 **금지** |
| **외부 의존성 최소** | Google Fonts만 허용 (상세 폰트 목록은 3.1. 참조). 그 외 CDN·라이브러리·아이콘 팩·CSS 프레임워크 **금지** |
| **중앙 영역 비움** | `focus-main-display` 영역에 기능 UI를 배치하지 않음. 시길·파티클·세션 시각화만 허용 |
| **영속화 가능 구조** | 현재는 메모리 기반이지만, **HTML 단계 내에서 이후 영속화 가능 구조로 수정 예정**. 신규 데이터 설계 시 영속화 전환을 전제로 함 |
| **클라이언트 전용** | 네트워크 호출 없음. 완전 로컬 동작 |

### 1.1. 아이콘·SVG 정책

외부 아이콘 라이브러리를 로드하지 않고 **인라인 SVG**로 작성합니다. 상세 스타일 규칙은 7. 아이콘 섹션 참조.

### 1.2. 에셋 정책

- **폰트**: Google Fonts만 허용 (3.1. 참조)
- **이미지**: 외부 이미지 로드 금지. 인라인 SVG 또는 이모지로 해결
- **사운드**: 현재 프로토타입은 사운드 파일을 로드하지 않음 (UI만 존재). 언리얼 전환 시 본격 에셋 도입

---

## 2. 색상 (Colors)

### 2.1. 배경 레이어

다크 테마 기반. 4단계 깊이로 공간감을 표현:

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--bg-deep` | `#06060a` | 가장 깊은 배경 (body 최하단) |
| `--bg` | `#0a0a0f` | 기본 배경 |
| `--surface` | `rgba(20,20,28,0.96)` | 패널·카드 표면 |
| `--surface2` | `rgba(28,28,38,0.9)` | 중첩 요소 (입력 필드, 칩) |
| `--surface3` | `rgba(36,36,50,0.85)` | 호버 강조 |

### 2.2. 텍스트 위계

3단계 불투명도로 정보 우선순위를 시각화:

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--text-primary` | `#f0ede8` (warm off-white) | 주요 텍스트 |
| `--text-secondary` | `rgba(240,237,232,0.5)` | 보조 텍스트 |
| `--text-muted` | `rgba(240,237,232,0.27)` | 힌트, 메타 레이블 |

### 2.3. 기능 식별색

각 기능·상태에 고유한 색 한 종류를 할당. 모든 식별색은 동일한 채도·명도 계열의 "따뜻한 파스텔" 팔레트에서 선정:

| 토큰 | 값 | 한글 이름 | 용도 |
|------|-----|---------|------|
| `--focus-c` | `#e8a87c` | **코랄** (peach) | **Focus 패널** 식별색 + 집중(focusing) 단계 |
| `--rest-c` | `#7cb8e8` | 스카이블루 | Focus 타이머 — **휴식**(resting) 단계 |
| `--lrest-c` | `#a87ce8` | 바이올렛 | Focus 타이머 — **마지막 휴식**(long rest) 단계 |
| `--sw-c` | `#7ce8a8` | 민트 | **Stopwatch** 식별색 |
| `--todo-c` | `#e8c87c` | 골드 | **Quests 패널** 식별색 |
| `--diary-c` | `#c79de8` | 라벤더 | **Archive 패널** 식별색 |

**`-dim` 변형**: 각 식별색에는 알파 약 `0.14`의 `-dim` 쌍이 있어 배경·칩·필 영역 등 약한 적용에 사용합니다 (예: `--focus-dim: rgba(232,168,124,0.14)`).

### 2.4. 상태색 (Semantic)

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--success` | `#7ce8a8` (민트, `--sw-c`와 동일) | 완료, 긍정 응답 |
| `--danger` | `#e87c7c` (살구 핑크) | 오류, 삭제, 지연(overdue) |

> **`--warn` 미정의**: 별도 경고 토큰은 정의되어 있지 않으며, 필요 시 `--gold-soft`(`#e8c87c`)가 경고 뉘앙스를 대체합니다. 향후 명시적 `--warn` 토큰 도입 검토.

### 2.5. 골드 악센트

길드 세계관의 시그니처 컬러. 별도 카테고리로 관리:

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--gold` | `#c9a959` | 골드 베이스 (보조 장식) |
| `--gold-soft` | `#e8c87c` | 부드러운 골드 (시길 글자, 플레이어 라벨) |
| `--gold-dim` | `rgba(201,169,89,0.12)` | 골드 배경 변형 |
| `--border-gold` | `rgba(201,169,89,0.25)` | 골드 테두리 |

### 2.6. 테두리

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--border` | `rgba(255,255,255,0.07)` | 기본 테두리 (중성) |
| `--border-gold` | `rgba(201,169,89,0.25)` | 강조 테두리 |
| `--glass-b` | `rgba(201,169,89,0.2)` | 글래스 요소 전용 테두리 |

### 2.7. 반복 인라인 컬러 패턴

토큰화되지 ��았지만 여러 시스템에서 반복 사용되는 RGBA 패턴. 일회성 값이 아닌 **의도된 패턴**으로, 일관성 유지를 위해 기록한다.

| 패턴 | 범위 | 용도 | 사용처 예시 |
|------|------|------|-----------|
| Hover white overlay | `rgba(255,255,255, 0.02~0.05)` | 인터랙티브 요소의 미묘한 호버 배경 | `.tl-todo-item`, `.todo-item`, `.tl-item` |
| Gold glow | `rgba(232,200,124, 0.2~1.0)` | 골드 계열 text-shadow, box-shadow | `.sigil-letter`, `.pl-art-glyph`, `.nav-dot` |
| Backdrop scrim | `rgba(0,0,0, 0.4~0.75)` | 모달·드롭다운 뒤 반투명 어둡게 | `.rec-modal-wrap`, `.cat-add-modal-wrap`, `.side-panel` box-shadow |

---

## 3. 타이포그래피 (Typography)

### 3.1. 사용 폰트

현재 프로토타입은 Google Fonts에서 **4종**을 로드합니다:

| 폰트 | 계열 | 로드 굵기 | 주 용도 |
|------|------|---------|-------|
| **Cinzel** | Serif | 400 / 500 / 600 / 700 | 장식 헤딩, 길드명, 섹션 레이블 |
| **DM Serif Display** | Italic Serif | `ital@0;1` | 시길 모토 (유일 사용처) |
| **DM Mono** | Monospace | 300 / 400 / 500 | 수치, 메타 레이블, 버튼 텍스트 |
| **Noto Sans KR** | Sans-serif | 300 / 400 / 500 | 기본 본문, 한국어 텍스트 |

### 3.2. 용도별 매핑

| 용도 | 폰트 | 예시 클래스 |
|------|------|---------|
| 본문 기본 | Noto Sans KR | body 기본, 퀘스트 제목 |
| 시간·수치 표시 | DM Mono | `clock-time`, `sm-time`, `p-time` |
| 소형 메타 레이블 | DM Mono + 대문자 + 넓은 레터스페이싱 | `nav-btn-label`, `clock-date`, `guild-sub` |
| 세계관 제목 | Cinzel | `guild-name`, `todo-hdr-title`, `ambient-title` |
| 시길 주 글자 | Cinzel 700 @ 80px | `sigil-letter` (G) |
| 시길 연도 (라틴 숫자) | Cinzel + 대문자 + 레터스페이싱 | `sigil-anno` (MMXXVI) |
| 시길 모토 (라틴어) | DM Serif Display, italic | `sigil-motto` |
| 시길 번역 (한글) | DM Mono + 대문자 + 넓은 레터스페이싱 | `sigil-trans` |

### 3.3. 타이포그래피 패턴 규칙

공통 규칙:

- **메타 레이블은 대문자 + 넓은 레터스페이싱**: `text-transform: uppercase; letter-spacing: 0.15em~0.3em`
- **수치는 monospace**: 자릿수 정렬과 시각적 안정감 확보
- **세계관 제목은 Cinzel**: 판타지·중세 톤 유지
- **본문은 Noto Sans KR**: 한국어 가독성 우선

---

## 4. 모서리·간격 (Corners & Spacing)

### 4.1. 모서리 반경 (Radius)

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--radius` | `13px` | 주요 컨테이너 (패널, 카드) |
| `--radius-sm` | `9px` | 작은 요소 (칩, 소형 버튼) |

### 4.2. 관찰된 간격 패턴

CSS 변수로 토큰화되어 있지는 않으나 코드 전반에서 반복 사용되는 간격:

| 간격 | 주요 용도 |
|------|---------|
| 4px | 매우 작은 gap (아이콘·텍스트 간격) |
| 6px | 칩 간격, 작은 리스트 행 간격 |
| 8~10px | 일반 요소 gap |
| 12~14px | 카드 내부 padding |
| 18~20px | 패널 내부 padding |
| 28~36px | 패널 위치 오프셋 (코너 여백) |

> **토큰화 검토**: 위 값들 중 자주 쓰이는 것(8/10/14/20)은 향후 `--space-xs` / `--space-sm` / `--space-md` / `--space-lg` 토큰 도입 검토 여지가 있습니다.

---

## 5. 모션 (Motion)

### 5.1. 기본 트랜지션

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--tr` | `0.25s cubic-bezier(0.4, 0, 0.2, 1)` | 전역 기본 트랜지션 |

**cubic-bezier(0.4, 0, 0.2, 1)** 은 Material Design의 "standard curve". 초기 빠른 반응 + 자연스러운 감속.

### 5.2. 애니메이션 키프레임 카탈로그

전체 7개 `@keyframes` 정의.

| 이름 | 주기 / 이징 | 정의 | 소유 시스템 |
|------|------------|------|-----------|
| `drift` | 10~20s linear | `0%{opacity:0; translateY(18px)}` → `15%{opacity:0.7}` → `85%{opacity:0.4}` → `100%{opacity:0; translateY(-40px)}` | 파티클 (ui/particles) |
| `spin` | 120s linear | `to{transform:rotate(360deg)}` | 시길 링 (guild/sigil) |
| `pdot` | 2s ease | `0%,100%{opacity:1}` → `50%{opacity:0.4}` | 알림 점 (todo-dot, nav-dot, sm-indicator) |
| `eq` | 0.9s ease | `0%,100%{height:3px}` → `50%{height:13px}` | EQ 바 (audio/music-player). 3개 바에 delay -0.4s, -0.2s, 0s |
| `glow` | 3s ease | `0%,100%{text-shadow:0 0 10px rgba(232,200,124,0.5)}` → `50%{text-shadow:0 0 22px rgba(232,200,124,1)}` | 앨범 아트 글리프 (audio/music-player, `.playing` 상태) |
| `blk` | 1s step-end infinite | `0%,100%{opacity:1}` → `50%{opacity:0.2}` | 타이머 일시정지 깜빡임 (focus/timer, `.blink` 상태) |
| `cdp` | 1.5s ease | `0%,100%{box-shadow:0 0 0 0 rgba(232,168,124,0.4)}` → `50%{box-shadow:0 0 0 4px rgba(232,168,124,0)}` | 현재 사이클 도트 펄스 (focus/timer, `.cdot.act`) |

### 5.3. 트랜지션 패턴 변형

`--tr` 외에 반복 사용되는 트랜지션 패턴들.

| 패턴 이름 | 값 | 적용처 |
|----------|-----|--------|
| 기본 `var(--tr)` | `0.25s cubic-bezier(0.4,0,0.2,1)` | 대부분의 인터랙티브 요소 |
| 패널 열기/닫기 | `opacity 0.25s ease, transform 0.25s ease, width 0.25s ease` | `.side-panel` |
| 오버레이 슬라이드 | `max-height 0.35s cubic-bezier(0.4,0,0.2,1), opacity 0.25s ease` | `.todo-overlay`, `.routine-panel` |
| 모드 스위치 인디케이터 | `transform 0.3s cubic-bezier(0.4,0,0.2,1)` | `.mode-sw-ind` |
| 프로그레스 필 | `width 0.25s linear` | `.p-fill` (음악), `.amb-fill` |
| SVG 스트로크 | `stroke-dashoffset 1s linear, stroke 0.4s` | `.ring-arc`, `.sw-ring-arc` |
| 모달 등장 | `opacity 0.3s ease` (래퍼) + `transform 0.25s ease` (내부) | `.rec-modal-wrap`, `.cat-add-modal-wrap` |

### 5.4. 모션 원칙

- **느리고 의도적**: 집중 도구 철학 반영. 화려한 모션 지양
- **사용자 액션 피드백은 즉시**, **배경 연출은 매우 느리게**: 주의를 분산시키지 않음
- **시길 회전 120초**: "시간의 흐름" 은유. 사용자가 인지하기 어려운 속도

---

## 6. 표면 효과 (Surface Effects)

### 6.1. 글래스 모피즘

글래스 패널은 이 프로토타입의 핵심 시각 언어입니다.

| 토큰 | 값 | 용도 |
|------|-----|------|
| `--glass` | `rgba(13,13,19,0.97)` | 글래스 배경 |
| `--glass-b` | `rgba(201,169,89,0.2)` | 글래스 테두리 (골드 톤) |

`.glass` 유틸리티 클래스:

```css
.glass {
  background: var(--glass);
  border: 1px solid var(--glass-b);
  border-radius: var(--radius);
  backdrop-filter: blur(24px);
  box-shadow:
    0 20px 60px rgba(0,0,0,0.45),
    inset 0 1px 0 rgba(255,255,255,0.04);
}
```

**적용되는 주요 요소**: `.todo-overlay`, `.nav-bar`, `.ambient-overlay`, `.music-player`, `.rec-modal` 등.

#### 블러 티어

`.glass` 클래스는 `blur(24px)`를 사용하지만, 시스템별로 4단계의 블러 강도가 존재한다.

| 블러 | 용도 | 적용 요소 |
|------|------|----------|
| `blur(10px)` | 모달 백드롭 (얇은 반투명) | `.rec-modal-wrap`, `.cat-add-modal-wrap` |
| `blur(20px)` | 드롭다운 (중간 강도) | `.player-tracks` |
| `blur(24px)` | 표준 `.glass` 패널 | `.guild-plate`, `.todo-overlay`, `.nav-bar`, `.music-player`, `.ambient-overlay`, `.cmd-btn` |
| `blur(28px)` | 대형 오버레이 (강한 블러) | `.side-panel` |

규칙: 면적이 넓을수록 블러를 강하게, 모달 백드롭은 내부 콘텐츠가 돋보이도록 약하게.

### 6.2. 배경 그라데이션 (스테이지)

메인 스테이지(1920×1080)의 배경은 세 레이어 합성:

1. **라디얼 골드 글로우** (화면 상단 중앙)
   ```
   radial-gradient(ellipse 60% 50% at 50% 44%,
     rgba(201,169,89,0.07) 0%, transparent 60%)
   ```
2. **수직 그라디언트** (전체)
   ```
   linear-gradient(180deg,
     #0d0d16 0%, #0a0a12 55%, #050508 100%)
   ```
3. **비네트 오버레이** (외곽 어둡게)
   ```
   radial-gradient(ellipse 85% 65% at 50% 50%,
     transparent 42%, rgba(0,0,0,0.6) 100%)
   ```

의도: 중앙 시길 영역을 시각 중심으로 끌어오고, 외곽의 기능 UI가 주의를 분산시키지 않게 함.

### 6.3. 그림자

주요 그림자는 `.glass` 클래스에 내장 (6.1. 참조). 별도 토큰화되어 있지 않음.

- 주 그림자: `0 20px 60px rgba(0,0,0,0.45)` (아래쪽 짙은 드롭)
- 내부 광택: `inset 0 1px 0 rgba(255,255,255,0.04)` (상단 하이라이트)

### 6.4. 상태 클래스 시스템

여러 시스템에서 공통으로 사용되는 CSS 상태 클래스. 각 클래스는 JS에서 `classList.toggle()`로 제어된다.

#### 가시성·레이아웃 상태

| 클래스 | 시각 효과 | 적용 대상 | 토글 주체 |
|--------|----------|----------|----------|
| `.open` | opacity 1 + transform 원위치 + pointer-events 활성 | `.side-panel`, `.ambient-overlay`, `.player-tracks`, `.rec-modal-wrap`, `.cat-add-modal-wrap`, `.panel-backdrop` | 각 open/close 함수 |
| `.visible` | display 활성 | `.session-mini` | `renderSessionMini()` |
| `.collapsed` | body max-height:0 + toggle 버튼 180° 회전 | `.todo-overlay` | `toggleTlTodos()` |
| `.has-todos` | max-height: 400px, 오버레이 보임 | `.todo-overlay` | `renderTlTodos()` |
| `.wide` | width: 760px (기본 460px 대신) | `.side-panel` | `openPanel('archive')` |
| `.show` | opacity 1 + translateY(0) | `.toast` | `showToast()` |

#### 인터랙션·선택 상태

| 클래스 | 시각 효과 | 적용 대상 | 토글 주체 |
|--------|----------|----------|----------|
| `.active` | 악센트 색상 배경/텍스트 강조 | `.nav-btn`, `.ambient-btn`, `.tl-item` | 각 활성화 함수 |
| `.on` | 탭/필터 활성 스타일 | `.qtab`, `.fchip` | 탭/필터 설정 함수 |
| `.sel` | 악센트 배경 채움 | `.cat-chip` | 카테고리 선택 함수 |

#### 데이터·상태 표현

| 클래스 | 시각 효과 | 적용 대상 | 결정 주체 |
|--------|----------|----------|----------|
| `.done` | 배경 채움, 체크마크, line-through | `.tl-todo-chk`, `.todo-item`, `.cdot`, `.arch-q-card` | `instanceStatus()`, 렌더 함수 |
| `.suspended` | 반투명, 이탤릭 | `.todo-item`, `.arch-q-card` | `instanceStatus()` |
| `.overdue` | danger 색상 하이라이트 | `.todo-item`, `.tbadge.dday`, `.arch-q-card` | `instanceStatus()`, `ddayInfoForKey()` |
| `.upcoming` | muted 스타일 | `.arch-q-card` | `instanceStatus()` |
| `.near` | 경고 컬러 (D-day ≤ 3) | `.tbadge.dday` | `ddayInfoForKey()` |
| `.today` | 하단 점 강조 | `.cal-day .day-num`, `.tbadge.dday` | 캘린더 렌더 |

#### 모드·페이즈 상태

| 클래스 | 시각 효과 | 적용 대상 | 토글 주체 |
|--------|----------|----------|----------|
| `.playing` | 앨범 아트 glow 애니메이션 활성 | `.music-player` | `togglePlay()` |
| `.blink` | 1s step-end 깜빡임 (opacity 1↔0.2) | `.ring-time` | `toggleTimerPause()` |
| `.pause` | 일시정지 인디케이터 스타일 | `.sm-indicator` | 타이머/스톱워치 일시정지 |
| `.rest`, `.lrest` | 페이즈별 색상 (sky, violet) | `.btn-primary` (타이머 컨트롤) | `enterPhase()` |
| `.sw` | 스톱워치 민트 색상 | `.btn-primary`, `.sm-indicator` | 스톱워치 모드 |
| `.focus` | 포커스 코랄 색상 | `.sm-indicator` | 타이머 집중 페이즈 |
| `.warn` | 경고 컬러 강조 | `.fchip` | 필터 칩 overdue 카운트 |

---

## 7. 아이콘 (Iconography)

### 7.1. 인라인 SVG 원칙

프로토타입은 아이콘 라이브러리를 외부에서 로드하지 않고 **인라인 SVG**로 각 요소에 직접 작성합니다.

**공통 스타일**:

- `viewBox="0 0 24 24"` (Feather/Lucide 호환 규격)
- `fill="none"` + `stroke="currentColor"` — 색상은 부모의 `color`를 상속
- `stroke-width`: 기본 **1.7~1.8** (절제된 선 두께)
- `stroke-linecap="round"`, `stroke-linejoin="round"` (부드러운 모서리)

**예외**: 재생·일시정지·이전·다음 등 미디어 플레이어 아이콘 일부는 `fill="currentColor"`로 채움 표현.

### 7.2. 이모지 사용

환경음 5종은 이모지로 표현:

- 🌧 Rain · 💨 Wind · 🔥 Fire · 🌲 Forest · 🌊 Ocean

언리얼 전환 시 커스텀 일러스트로 교체 예정.

---

## 8. 공용 UI 컴포넌트

여러 시스템에서 **재사용되는 UI 패턴**의 공식 스펙. 개별 시스템(`approved/*.md`)은 이 섹션의 토큰·규칙을 참조하고, 여기 없는 시스템 전용 컴포넌트만 각자 정의합니다.

### 8.1. 버튼 (Buttons)

**공통 베이스** — `.btn`:

```css
.btn {
  border: none;
  cursor: pointer;
  font-family: 'Noto Sans KR', sans-serif;
  font-size: 13px;
  font-weight: 500;
  border-radius: 9px;
  transition: var(--tr);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 7px;
}
```

**변형별 규격**:

| 변형 | 용도 | 크기 | 배경 | 텍스트 | 특징 |
|------|------|------|------|--------|------|
| `.btn-primary` | 주 액션 (저장·확인·시작) | flex:1 · h:46px | `--focus-c` (코랄) | `#1a0e00` (거의 검정) | font-weight 600 |
| `.btn-secondary` | 보조 액션 (취소·뒤로) | w:46px · h:46px 정사각 | `--surface2` | `--text-secondary` | `--border` 테두리 |
| `.btn-flat` | 인라인 액션 (편집·추가) | h:36px · padding:0 14px | `--surface2` | `--text-secondary` | `--border` 테두리, 키 큰 가로형 |
| `.btn-danger` | 삭제·중단 | (다른 변형에 덧붙임) | — | `--danger` `!important` | 색상만 변경 |
| `.btn-icon` | 아이콘 단독 버튼 | 18×18px | — | 상속 | flex center |

**그룹 레이아웃**: `.btn-row { display: flex; gap: 10px; margin-top: 4px; }`

**모달 전용 변형** (record-modal 내부):

| 클래스 | 역할 | 비고 |
|-------|-----|------|
| `.btn-rm-save` | 저장 | `--focus-c` 배경, flex:1, h:42px |
| `.btn-rm-skip` | 건너뛰기 | `--surface2` 배경, h:42px |

> 신규 버튼이 필요할 때는 위 변형 중 하나를 조합하거나 `.btn-flat`을 기본으로 파생하세요. **임의 색상·크기 버튼 금지**.

---

### 8.2. 입력 필드 (Form Inputs)

**공통 규격** — `.form-input`, `.form-textarea`:

```css
.form-input, .form-textarea {
  width: 100%;
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 8px;
  color: var(--text-primary);
  font-family: 'Noto Sans KR', sans-serif;
  font-size: 13px;
  padding: 9px 12px;
  outline: none;
  transition: border-color 0.2s;
  margin-bottom: 12px;
}

.form-textarea {
  resize: none;
  min-height: 70px;
  line-height: 1.6;
}
```

**상태 규칙**:

- 기본: `--border` 테두리
- 포커스: 테두리를 식별색(`--focus-c` 등)으로 전환 (각 시스템이 선택)
- 에러: `--danger` 테두리

**관련 레이블**:

| 클래스 | 용도 | 스타일 |
|-------|------|-------|
| `.field-label` | 필드 바로 위 라벨 | 13px, `--text-secondary` |
| `.sec-label` | 섹션 구분용 레이블 | DM Mono 9px, 대문자, 넓은 레터스페이싱, `--text-muted` |
| `.field-row` | 레이블-필드 수평 배치 | flex 컨테이너 |
| `.form-section-label` | 생성 폼 등의 섹션 제목 | (HTML 참조) |
| `.form-toggle-hint` | 토글 옆 힌트 텍스트 | (HTML 참조) |

---

### 8.3. 토글 (Toggle Switch)

```css
.toggle {
  position: relative;
  width: 40px;
  height: 22px;
  flex-shrink: 0;
}
.toggle-slider {
  position: absolute;
  inset: 0;
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 11px;
  cursor: pointer;
  transition: var(--tr);
}
```

- 비활성: `--surface2` 배경
- 활성: 식별색으로 배경 전환 (각 시스템이 지정)

---

### 8.4. 칩 (Chip)

카테고리 선택·태그 등에 사용되는 pill 형태 요소:

```css
.cat-chip {
  display: flex;
  align-items: center;
  gap: 5px;
  padding: 4px 10px;
  background: var(--surface2);
  border: 1px solid var(--border);
  border-radius: 14px;
  font-size: 11px;
  color: var(--text-secondary);
  cursor: pointer;
  transition: var(--tr);
  font-family: 'Noto Sans KR', sans-serif;
}
```

**상태**:

- 기본: `--surface2` 배경
- 선택됨 (`.sel`): 식별색으로 배경 전환
- 삭제 버튼: `.cat-chip-del` (hover 시 `--danger` 강조)

**컨테이너**: `.cat-chips-wrap`은 flex-wrap으로 자동 줄바꿈

---

### 8.5. 컴포넌트 사용 원칙

1. **색상은 항상 토큰 참조**: `.btn-primary` 배경은 직접 hex가 아니라 `var(--focus-c)`. 새 색이 필요하면 2.3. 기능 식별색에 먼저 등록
2. **높이는 46px(주요) / 42px(모달) / 36px(보조) / 22px(토글)** 4단계를 따름 — 임의 높이 값 금지
3. **border-radius는 8~14px 범위**: 8(입력), 9(버튼), 11(토글), 14(칩)
4. **폰트**: 버튼·입력·칩 본문은 Noto Sans KR. 메타 레이블(`.sec-label` 등)만 DM Mono + 대문자

---

## 9. 전역 UI 요소

고정 섹션에 속하지 않고 **모든 화면에서 트리거 기반으로 등장**하는 시각 요소. `project-dictionary.md` 0.3. 결정 규칙 4에 따라 이 문서가 정의를 담당합니다.

### 9.1. toast (알림)

- **정의**: 짧은 피드백 메시지를 화면에 잠시 띄우는 전역 알림 UI
- **코드상 컨테이너**: `.toast` (id: `toast`)
- **트리거**: 저장 성공, 카테고리 추가, 경량 오류 알림 등
- **예상 표시 시간**: 2~3초 (자동 소멸)
- **스타일**: `.glass` 기반 + 축소·페이드 트랜지션

> 현재 별도 시스템 문서 없음. 사용처가 늘거나 규칙이 복잡해지면 `project-wiki/toast.md` 승격 검토.

---

## 10. 관련 문서

| 문서 | 다루는 것 |
|------|---------|
| `page-architecture.md` | 페이지 레이아웃 좌표, z-index 계층, 초기화 시퀀스, 글로벌 함수·이벤트 (구조·동작 인프라) |
| `project-docs-guide.md` | 문서 작성 공통 규칙 |
| `project-identity.md` | 프로젝트 정체성, UX 원칙, 미래 방향성 |
| `project-dictionary.md` | UI 명칭 정의 (이 문서의 식별색 토큰을 참조) |
| `project-data.md` | 데이터 스키마 (예정) |
| `project-wiki/` | 각 시스템의 시각 적용 세부 |

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|---------|
| 2026-04-24 | 초안 작성 (`design-style-guide.md`). `guild-master-desktop.html`의 실제 CSS 토큰 기반. 4종 폰트·6종 기능 식별색·글래스 모피즘·스테이지 배경 그라데이션 정리 |
| 2026-04-24 | 파일명을 `html-design-style-guide.md`로 변경. **섹션 1. HTML 프로토타입 작업 제약** 신설 (기존 `project-identity.md` 섹션 5에서 이전). 기존 섹션 1~8은 2~9로 번호 이동. 문서 정체성을 "HTML 프로토타입 전용"으로 명확히 함 |
| 2026-04-24 | 3차 수정 — **섹션 8. 공용 UI 컴포넌트** 신설. 버튼(5종 변형)·입력 필드·토글·칩의 공식 스펙을 HTML 실제 CSS에서 추출해 공식화. 기존 섹션 8(전역 UI), 9(관련 문서)는 9, 10으로 번호 이동. 컴포넌트 사용 원칙 4가지(색상 토큰 참조·높이 4단계·radius 범위·폰트 규칙) 추가 |
| 2026-04-25 | **구조 보강** — §0.1 scope에 `page-architecture.md` 경계 명시, §2.7 반복 인라인 컬러 패턴, §5.2 애니메이션 키프레임 카탈로그(7개 전체), §5.3 트랜지션 패턴 변형, §6.1 블러 티어(4단계), §6.4 상태 클래스 시스템(23+개) 추가. 기존 §5.3 모션 원칙은 §5.4로 이동 |

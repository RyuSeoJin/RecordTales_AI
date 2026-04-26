# Guild Info (Guild Plate)

> **문서 성격**: `Guild` 시스템의 **Guild Plate** UI 컴포넌트 스펙.
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

- **한 줄 정의**: 길드 문장, 이름, 레벨, 칭호, XP 진행률, 골드를 동적으로 표시하는 길드 정보 플레이트
- **위치**: 좌상단 영역 (`daily-status-panel`) — `.tl-block` 내부 최상단
- **구현 상태**: ✅ 구현 완료 (동적 데이터 바인딩)

---

## 2. UI 구조

### 2.1. 와이어프레임

```
┌─ .tl-block (top:28px, left:36px, 296px) ─────────┐
│ ┌─ .glass.guild-plate ──────────────────────────┐ │
│ │ ┌──────────┐  ┌─ .guild-info ───────────────┐ │ │
│ │ │          │  │ .guild-name: "Grimhollow"    │ │ │
│ │ │  G       │  │ ┌──────────┐ ┌────────────┐ │ │ │
│ │ │ (crest)  │  │ │ Lv.1     │ │ 견습 모험가│ │ │ │
│ │ │ 46x46    │  │ └──────────┘ └────────────┘ │ │ │
│ │ └──────────┘  │ 💰 5,000                    │ │ │
│ │               └─────────────────────────────┘ │ │
│ │ ┌─ .guild-xp-wrap ─────────────────────────┐  │ │
│ │ │ ██████░░░░░░░░░░░░  XP: 0 / 100          │  │ │
│ │ └───────────────────────────────────────────┘  │ │
│ └───────────────────────────────────────────────┘ │
│ ┌─ .todo-overlay ───────────────────────────────┐ │
│ │ (오늘의 퀘스트 목록 — 별도 문서)              │ │
│ └───────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────┘
```

### 2.2. CSS 클래스 구조

```
.tl-block (position:absolute, top:28px, left:36px, z-index:20, width:296px)
└── .glass.guild-plate (display:flex, flex-wrap:wrap, gap:14px, padding:14px 18px)
    ├── .guild-crest (46x46, border-radius:11px)
    │   └── .sigil-letter ("G" 텍스트, level-up-flash 클래스 토글)
    ├── .guild-info (flex-direction:column, gap:3px)
    │   ├── .guild-name ("Grimhollow")
    │   ├── .guild-lvl-row (display:flex, gap:8px)
    │   │   ├── .guild-lvl #guildLvl (동적 레벨 표시)
    │   │   └── .guild-sub #guildSub (동적 칭호 표시)
    │   └── .guild-gold #guildGold (💰 골드 표시)
    └── .guild-xp-wrap (width:100%)
        ├── .guild-xp-bar #guildXpBar (XP 진행률 바)
        └── .guild-xp-text #guildXpText (XP 수치 텍스트)
```

### 2.3. 시각 요소 상세

| 요소 | 속성 |
|------|------|
| `.guild-crest` | 46x46px, `border-radius: 11px`, `background: linear-gradient(135deg, rgba(201,169,89,0.22), rgba(201,169,89,0.05))`, `border: 1px solid rgba(201,169,89,0.35)`, `Cinzel 18px bold`, `color: var(--gold-soft)` |
| `.sigil-letter` | 문장 내부 글자 ("G"), 레벨업 시 `level-up-flash` 클래스 추가 |
| `.guild-name` | `Cinzel 16px`, `font-weight: 600`, `letter-spacing: 0.1em`, `color: var(--text-primary)` |
| `.guild-lvl` | `DM Mono 9px`, `letter-spacing: 0.15em`, uppercase, `color: var(--gold-soft)`, `background: var(--gold-dim)`, `border: 1px solid rgba(201,169,89,0.3)`, `padding: 2px 8px`, `border-radius: 6px` |
| `.guild-sub` | `DM Mono 9px`, `letter-spacing: 0.15em`, uppercase, `color: var(--text-muted)` |
| `.guild-gold` | `DM Mono 9px`, `color: var(--gold-soft)` |
| `.guild-xp-wrap` | `width: 100%`, `height: 3px`, `background: rgba(201,169,89,0.10)`, `border-radius: 2px`, `overflow: hidden` |
| `.guild-xp-bar` | `height: 100%`, `background: linear-gradient(90deg, var(--gold-soft), var(--gold))`, `border-radius: 2px`, `transition: width 0.6s ease`, width는 퍼센트 동적 설정 |
| `.guild-xp-text` | `DM Mono 8px`, `color: var(--text-muted)`, `margin-top: 2px` |

---

## 3. 데이터 모델

### 3.1. 전역 상태

| 변수 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `A.guildLevel` | `number` | `1` | 길드 레벨 (1~10) |
| `A.guildXp` | `number` | `0` | 현재 레벨 내 누적 XP |
| `A.gold` | `number` | `5000` | 보유 골드 |

### 3.2. 상수

| 상수 | 설명 |
|------|------|
| `GUILD_TITLES[level]` | 레벨별 칭호 맵 (예: 1 = "견습 모험가", …) |
| `XP_TABLE[level]` | 레벨별 필요 XP 테이블 |

### 3.3. DOM 바인딩

| DOM id | 표시 내용 |
|--------|-----------|
| `#guildLvl` | `Lv.${A.guildLevel}` |
| `#guildSub` | `GUILD_TITLES[A.guildLevel]` |
| `#guildGold` | `💰 ${A.gold.toLocaleString()}` |
| `#guildXpBar` | `width: ${percentage}%` |
| `#guildXpText` | `XP: ${A.guildXp} / ${needed}` |

#### 정적 하드코딩 값 (변경 없음)

| 필드 | 값 | 설명 |
|------|-----|------|
| 문장 글자 | `G` | `.sigil-letter` 내부 텍스트 |
| 길드명 | `Grimhollow` | `.guild-name` 텍스트 |

---

## 4. 동작 규칙

### 4.1. 상태 전이

```
[페이지 로드] ──▶ updateGuildLevelDisplay() + updateGoldDisplay()
                       │
    ┌──────────────────┤
    ▼                  ▼
[XP 획득]         [골드 변동]
    │                  │
    ▼                  ▼
updateGuildLevel   updateGoldDisplay()
Display()              │
    │                  ▼
    ├──▶ XP bar 갱신   [골드 텍스트 갱신]
    ├──▶ 레벨/칭호 갱신
    └──▶ 레벨업 시 triggerLevelUpAnimation()
```

### 4.2. 핵심 로직

- **`updateGuildLevelDisplay()`**: `A.guildLevel`과 `A.guildXp` 기반으로 `#guildLvl` 텍스트, `#guildSub` 칭호, `#guildXpBar` width 퍼센트, `#guildXpText` 수치를 갱신
- **`updateGoldDisplay()`**: `A.gold` 값을 `toLocaleString()` 포맷하여 `#guildGold` 텍스트 갱신
- **`triggerLevelUpAnimation()`**: `.sigil-letter`에 `level-up-flash` 클래스를 추가하여 레벨업 시각 효과 발동, 애니메이션 종료 후 클래스 제거
- `.glass` 클래스로 글래스모피즘 배경 효과 적용

### 4.3. 함수 매핑

| 함수 | 트리거 | 설명 |
|------|--------|------|
| `updateGuildLevelDisplay()` | 페이지 로드, XP 변동 시 | 레벨·칭호·XP bar·XP 텍스트 일괄 갱신 |
| `updateGoldDisplay()` | 페이지 로드, 골드 변동 시 | 골드 텍스트 갱신 |
| `triggerLevelUpAnimation()` | 레벨업 발생 시 | sigil-letter에 flash 애니메이션 |

---

## 5. 사용자 상호작용

### 5.1. 조작 방법

| 액션 | 결과 |
|------|------|
| 레벨업 발생 | 레벨·칭호 자동 갱신, XP bar 리셋, sigil flash 애니메이션 재생 |
| XP 획득 | XP bar 너비 애니메이션(0.6s ease)으로 진행률 갱신 |
| 골드 변동 | 골드 텍스트 자동 갱신 (`toLocaleString` 포맷) |

### 5.2. 키보드 단축키

없음.

---

## 6. 관련 시스템

| 시스템 | 관계 |
|--------|------|
| [experience-system](../../experience/draft/experience-system.md) | XP 획득·레벨업 로직 제공 → Guild Plate에 반영 |
| [gold-economy](../../economy/) | 골드 변동 시 `updateGoldDisplay()` 호출 |
| unlock-shop | 골드 소비 → 골드 표시 갱신 연동 |
| [Todo Overlay](../../quests/ui/today-todo-overlay.md) | 동일 `.tl-block` 컨테이너 내 하위에 위치 |

---

## 업데이트 이력

| 날짜 | 내용 |
|------|------|
| 2026-04-25 | 초안 작성 (정적 하드코딩 상태) |
| 2026-04-27 | 동적 데이터 바인딩 반영 — A.guildLevel/A.guildXp/A.gold 전역 상태, XP bar, 골드 표시, 레벨업 애니메이션 문서화. 하드코딩 Lv.7 → 동적 Lv.${A.guildLevel} 변경 |

---

> **최종 수정**: 2026-04-27

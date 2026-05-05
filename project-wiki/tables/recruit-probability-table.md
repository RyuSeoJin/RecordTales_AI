# 길드원 모집 확률 테이블 (Recruit Probability Table) — v2

> **문서 성격**: 길드 레벨별 모집 등급 출현 확률 데이터 시트.
> 코드(`RecordTales.html`)의 `RARITY_BY_LEVEL_V2` 및 `getRarityWeights(level)`과 1:1 대응.
> 작성 규칙은 `project-docs-guide.md` 참조.

---

## 1. 현재 적용 확률 (레벨별 동적)

v2에서는 길드 레벨에 따라 확률이 **동적으로 변동**한다. 균등 확률(테스트용)은 폐지되었다.

### 1.1. RARITY_BY_LEVEL_V2 — 7단계 Bracket

| Bracket (레벨 이상) | Normal | Rare | Epic | Legendary |
|---------------------|--------|------|------|-----------|
| Lv.1 | 80% | 20% | 0% | 0% |
| Lv.3 | 60% | 35% | 5% | 0% |
| Lv.5 | 45% | 40% | 13% | 2% |
| Lv.8 | 30% | 40% | 25% | 5% |
| Lv.12 | 15% | 35% | 35% | 15% |
| Lv.16 | 5% | 25% | 40% | 30% |
| Lv.20 | 0% | 15% | 45% | 40% |

> **합계**: 각 bracket 100% 유지.
> **bracket 적용**: 현재 레벨 이하의 가장 큰 bracket 키를 사용.

### 1.2. 등급별 색상

| 등급 | Stars | 색상 |
|------|-------|------|
| Normal | ★ | (기본) |
| Rare | ★★ | #7cb8e8 |
| Epic | ★★★ | #a87ce8 |
| Legendary | ★★★★ | #e8a05c |

### 1.3. 1회 모집(3명 후보) 기대치 (Lv.1 기준)

| 조건 | 확률 |
|------|------|
| 3명 모두 Normal | 51.2% |
| Rare 이상 최소 1명 | 48.8% |
| Epic 이상 최소 1명 | 0% |
| Legendary 최소 1명 | 0% |

### 1.4. 1회 모집 기대치 (Lv.5 기준)

| 조건 | 확률 |
|------|------|
| 3명 모두 Normal | 9.1% |
| Rare 이상 최소 1명 | 90.9% |
| Epic 이상 최소 1명 | 34.2% |
| Legendary 최소 1명 | 5.9% |

### 1.5. 1회 모집 기대치 (Lv.20 기준)

| 조건 | 확률 |
|------|------|
| 3명 모두 Normal | 0% |
| Rare 이상 최소 1명 | 100% |
| Epic 이상 최소 1명 | 98.4% |
| Legendary 최소 1명 | 78.4% |

---

## 2. Idle 화면 확률표 표시

v2에서는 모집 대기(Idle) 화면 상단에 현재 모집 확률표가 표시된다.

```
┌──────────────────────────────────────┐
│ 길드원 모집 확률              Lv.5   │
│ [Normal 45%] [Rare 40%] [Epic 13%] [Legendary 2%] │
└──────────────────────────────────────┘
```

CSS 클래스: `.hire-rarity-rates`, `.hire-rar-bar`

---

## 3. 능력치 범위 (등급별)

> `guild-member.md`에서 정의된 값. 여기서는 확률과 함께 참조용으로 기재.

| 등급 | 초기 능력 개수 | 수치 범위 (min~max) |
|------|---------------|-------------------|
| Normal ★ | 1개 | 3 ~ 8 |
| Rare ★★ | 1개 | 5 ~ 12 |
| Epic ★★★ | 2개 | 8 ~ 15 |
| Legendary ★★★★ | 2개 | 12 ~ 20 |

---

## 4. 코드 매핑

```javascript
// RecordTales.html 내 RARITY_BY_LEVEL_V2 (레벨별 동적 확률)
const RARITY_BY_LEVEL_V2={
  1:  [80,20, 0, 0],
  3:  [60,35, 5, 0],
  5:  [45,40,13, 2],
  8:  [30,40,25, 5],
  12: [15,35,35,15],
  16: [ 5,25,40,30],
  20: [ 0,15,45,40],
};

function getRarityWeights(level){
  const brackets=Object.keys(RARITY_BY_LEVEL_V2).map(Number).sort((a,b)=>b-a);
  const bracket=brackets.find(b=>level>=b)||1;
  return RARITY_BY_LEVEL_V2[bracket];
}
```

- `getRarityWeights(level)` 호출 시 현재 레벨에 맞는 확률 배열 반환
- `RARITY_WEIGHTS` 배열의 `weight` 값이 동적으로 갱신됨 (`applyRarityWeightsByLevel`)

---

## 5. 설계 원칙

- **weight 합산은 항상 100** 유지
- Lv.1은 Normal 80% — 초기에는 Normal 위주로 수집 시작
- Lv.20은 Normal 0% — 최고 레벨에서는 항상 Rare 이상 확보
- Legendary 최대 확률: **40%** (Lv.20) — 충분한 레벨업 보상 제공
- bracket 간 변동은 **점진적** — 급격한 변화 방지

---

## 📝 업데이트 이력

| 날짜 | 변경 내용 |
|------|----------|
| 2026-04-26 | 초안 작성. Lv.1 기준 확률(60/25/12/3) 및 기대치 산출. |
| 2026-04-29 | 코드 기준 현재 확률을 균등 확률(25/25/25/25, 테스트용)로 반영. |
| 2026-05-06 | v2 전면 개편. 균등 확률 → 7단계 bracket 동적 확률, idle 확률표 표시, 레벨별 기대치 산출. |

# 공방 UI 재설계 계획서

> 작성일: 2026-03-12 | 최종 수정: 2026-03-12 | 상태: ✅ 구현 완료

---

## 목표

기존 사이드바+상세패널 구조를 → **SVG 원근 골목 씬 + 시설 문 자유 배치** 방식으로 전면 재설계한다.

---

## 인터뷰 확정 답변

| 항목 | 결정 |
|------|------|
| 골목 레이아웃 | SVG 인라인 씬 — 원근 수렴 구조 (소실점 중앙 상단) |
| 시설 표현 | 나무문 스타일 카드 — 6종 문 변형 + 간판 + 아이콘 |
| 카드 배치 | 자유 배치 — WF_LAYOUT 절대 위치 (좌/우 벽면 불규칙 배치) |
| 진입 전환 | 즉시 전환 — display:none ↔ display:flex |
| 빠른 이동 | 세로 리스트형 미니 버튼 — 좌측 배치 (#wf-fac-nav) |
| 시설 상세 | 시설별 배경 CSS 아트 + 정보 오버레이 |
| 미개방 처리 | 불투명도 감소 + 🔒 자물쇠, 클릭은 허용 (상세에서 개방) |
| 골목 복귀 버튼 | 시설 상세 상단 "← 공방 골목으로" |
| 제작 UX | 꾸욱 누르기 2초 hold-to-craft + 진행 바 + 확인창 |

---

## 컴포넌트 구조 (최종 구현)

```
#hp-workshop
├── #wf-alley           (골목 뷰 — 기본 표시)
│   ├── #wf-alley-svg   (SVG 원근 씬 배경, z-index:0)
│   ├── .wf-alley-name  (골목 이름 오버레이, z-index:5)
│   └── #wf-alley-cards (시설 카드 컨테이너, z-index:2)
│       └── .wf-card    (절대 위치, WF_LAYOUT 기반)
│           ├── .wf-card-sign  (간판)
│           └── .wf-card-body  (아이콘 + 시설명 + 배지)
└── #wf-facility        (시설 상세 뷰 — 진입 시 표시, display:flex)
    ├── #wf-fac-nav     (좌측 빠른 이동 패널, 185px, border-right)
    │   └── .wf-nav-btn (아이콘 + 시설명 + 배지)
    ├── #wf-fac-left    (중앙 시설 정보 영역, flex:1)
    │   ├── #wf-fac-bg-art  (배경 CSS 아트)
    │   ├── .wf-fac-back    (← 공방 골목으로 버튼)
    │   └── #wf-fac-content (renderFacilityDetail 내용)
    └── #wf-craft-panel (우측 제작 패널, 210px, 레시피 클릭 시 표시)
        ├── 레시피명
        ├── 필요 재료 목록
        ├── 수량 조절 (−/+)
        └── 꾸욱 눌러 제작 버튼 (hold-to-craft)
```

---

## 시설 메타데이터 (WF_META / WF_LAYOUT)

| 키 | 시설명 | 아이콘 | 문 스타일 | 배치 위치 |
|----|--------|--------|----------|----------|
| workbench | 기본 작업대 | 🔨 | 기본 나무문 | 왼쪽 top 4% |
| armor | 방어구 제작소 | 🛡️ | 철문 | 왼쪽 top 18% |
| tinkerer | 땜장이 작업소 | 🔧 | 기본 나무문 | 왼쪽 top 43% |
| recipe | 레시피 보관함 | 📚 | 기본 나무문 | 왼쪽 top 56% |
| weapon | 무기 제작소 | ⚔️ | 철문 | 왼쪽 top 68% |
| slime | 애완슬라임 | 🐸 | 유기 문 | 오른쪽 top 4% |
| altar | 정화의 제단 | ✨ | 성스러운 문 | 오른쪽 top 18% |
| consumable | 연금술 실험실 | 🧪 | 비전 문 | 오른쪽 top 43% |
| synthesis | 아이템 합성소 | ⚗️ | 청록 문 | 오른쪽 top 56% |
| scroll | 주문서 제작소 | 📜 | 비전 문 | 오른쪽 top 68% |

---

## SVG 씬 구성 요소

| 요소 | 설명 |
|------|------|
| 배경 rect | fill #030208 |
| 왼쪽 벽면 polygon | 원근 수렴, 벽돌 수평·수직선 |
| 오른쪽 벽면 polygon | 원근 수렴, 벽돌 수평·수직선 |
| 바닥 polygon | 원근 격자선 |
| 소실점 글로우 | radialGradient 황갈색, cx 50% cy 17% |
| 횃불 4개 | 좌/우 벽 상·하단, CSS .wf-t1~4 깜박임 |
| 랜턴 | 중앙 천장 체인 매달림 |
| 실루엣 | 소실점 근처 후드 인물 |
| 소품 | 왼쪽 바닥 통 2개, 오른쪽 나무상자 2개 |
| 안개 | 하단 linearGradient 덮개 |
| 비네트 | 상하단 어두움 처리 |

---

## 제작 UX (Hold-to-Craft) 상세

- 버튼 `onmousedown` / `ontouchstart` → `startCraftHold()` 호출
- requestAnimationFrame 2회 후 CSS transition `width: 2s linear 100%` 시작
- setTimeout 2000ms → `completeCraft()` 호출
- `onmouseup` / `onmouseleave` / `ontouchend` → `cancelCraftHold()` (타이머 취소 + 바 리셋)
- `completeCraft()` → `#wf-craft-result` 모달 표시
- 확인창 닫기: Enter / ESC (keydown 전역 리스너) / 확인 버튼

---

## 구현 완료 파일

- `wireframe.html` (CSS + HTML + JS)

## 변경 이력

| 날짜 | 내용 |
|------|------|
| 2026-03-12 | 인터뷰 5문5답 진행, plan-spec 작성 |
| 2026-03-12 | 골목 뷰 초기 구현 (2열 그리드 + 나무문 스타일) |
| 2026-03-12 | 미개방 시설 클릭 버그 수정 |
| 2026-03-12 | 벽돌 배경 + 횃불 flickering 추가 |
| 2026-03-12 | 시설 상세 배경 CSS 아트 추가 |
| 2026-03-12 | 제작 패널 (레시피 재료 + 수량) 추가 |
| 2026-03-12 | 빠른 이동 패널 오른쪽 → 왼쪽 이동 |
| 2026-03-12 | SVG 원근 씬 + WF_LAYOUT 자유 배치로 전면 교체 |
| 2026-03-12 | 제작 UX → hold-to-craft 2초 + 확인창 |

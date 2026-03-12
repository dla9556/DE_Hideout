# 공방 UI 재설계 계획서

> 작성일: 2026-03-12 | 기반: 인터뷰 확정 답변

---

## 목표

기존 사이드바+상세패널 구조를 → **은신처 골목 분위기의 시설 진입 방식**으로 전면 재설계한다.

---

## 인터뷰 확정 답변

| 항목 | 결정 |
|------|------|
| 골목 레이아웃 | 세로 골목 — 안쪽으로 깊어지는 원근감 있는 거리 |
| 시설 표현 | 아이콘 카드 — 큰 이모지/아이콘 + 시설명 + 레벨 표시 |
| 진입 전환 | 슬라이드 인 — 시설 상세가 오른쪽에서 슬라이드 |
| 빠른 이동 버튼 | 아이콘 + 이름 — 세로 리스트형 미니 버튼 (우측 배치) |
| 시설 상세 | 시설 내부 배경 + 정보 오버레이 |
| 미개방 처리 | 어두운 처리 + 🔒 자물쇠 아이콘 |
| 골목 복귀 버튼 | 시설 상세 헤더 좌측 "← 공방 골목으로" |

---

## 컴포넌트 구조

```
#hp-workshop
├── #wf-alley           (골목 뷰 — 기본 표시)
│   ├── .wf-alley-header  (제목: "공방 골목")
│   └── #wf-alley-cards   (2열 그리드 — 10개 시설 카드)
│       └── .wf-card      (아이콘 + 시설명 + 레벨 배지)
│           └── .wf-card-lock  (미개방 시 🔒 표시)
└── #wf-facility        (시설 상세 뷰 — 진입 시 표시)
    ├── #wf-fac-left    (시설 정보 영역, flex:1)
    │   ├── #wf-fac-glow    (배경 컬러 글로우 효과)
    │   ├── .wf-fac-back    (← 공방 골목으로 버튼)
    │   └── #wf-fac-content (기존 renderWfDetail 내용)
    └── #wf-fac-nav     (우측 빠른 이동 패널, 185px)
        └── .wf-nav-btn (아이콘 + 시설명 + 배지)
```

---

## 시설 메타데이터

| 키 | 시설명 | 아이콘 | 글로우 색상 |
|----|--------|--------|------------|
| workbench | 기본 작업대 | 🔨 | #c07820 |
| slime | 애완슬라임 | 🐸 | #20b840 |
| weapon | 무기 제작소 | ⚔️ | #6090d0 |
| armor | 방어구 제작소 | 🛡️ | #8090a8 |
| consumable | 연금술 실험실 | 🧪 | #a040d0 |
| scroll | 주문서 제작소 | 📜 | #5040d0 |
| tinkerer | 땜장이 작업소 | 🔧 | #c06020 |
| altar | 정화의 제단 | ✨ | #d0c040 |
| synthesis | 아이템 합성소 | ⚗️ | #20c0c0 |
| recipe | 레시피 보관함 | 📚 | #c0a840 |

---

## 구현 단계

1. CSS: 알레이 배경, 카드, 시설 상세, 빠른 이동 스타일 추가
2. HTML: `#hp-workshop` 내부 구조 교체
3. JS: 새 함수 `showAlley`, `enterFacility`, `renderFacilityDetail`, `renderFacilityNav`, `renderWorkshopAlley` 작성
4. JS: 기존 `openFacility`, `confirmUpgrade`, `renderWorkshopSidebar` 업데이트

---

## 변경 파일

- `wireframe.html` (CSS + HTML + JS)

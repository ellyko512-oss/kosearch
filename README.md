# 쇼핑, 다음을 이어주는 검색

Daum AI 쇼핑 검색 컨셉 프로토타입입니다.
상품을 **찾아주는 것에서 멈추지 않고**, 구매 이후의 행동(로컬)까지 이어주는 검색 경험을 다룹니다.

> 🔗 **데모:** https://ellyko512-oss.github.io/kosearch/

---

## 화면 흐름

| # | 화면 | 설명 |
|---|------|------|
| 1 | 홈 | 워드마크 + 그라디언트 검색바만 남긴 최소 구성 |
| 2 | AI 검색 인트로 | 브랜드 그라디언트 10% 배경 · 로고 마크 · 자연어 질의 입력 |
| 3 | 검색 결과 | 조건 반영 결과를 **확인 / 보류**로 구분해 표시 |
| 4 | 구매 | 바로구매 → 완료 시트 |
| 5 | 로컬 연결 | 구매 완료 후에만 러닝 코스 섹션이 펼쳐짐 (쇼핑 → 로컬) |

핵심 원칙은 **"모르는 건 추정하지 않는다"** 입니다.
스펙 정보가 없는 조건(예: 내구성)은 그럴듯하게 채우지 않고 `보류`로 남깁니다.

---

## 파일 구조

```
.
├── index.html      # 프로토타입 + 설명 패널 전체 (단일 파일)
├── assets/         # 이미지 자산
├── .nojekyll       # GitHub Pages의 Jekyll 처리 비활성화
├── .gitignore
└── README.md
```

빌드 과정, 패키지 매니저, 외부 의존성이 **전혀 없습니다.** `index.html`을 브라우저로 열면 그대로 동작합니다.

---

## 디자인 토큰

디자인 시안(Figma SVG)에서 추출한 값입니다.

### 브랜드 그라디언트

```css
linear-gradient(100deg,
  #4FA4FF  8.65%,
  #E28EFF 30.77%,
  #FF71A4 54.81%,
  #FFA841 78.85%,
  #FFC236  100%);
```

| 토큰 | HEX | 용도 |
|------|-----|------|
| Grad 1–5 | `#4FA4FF` `#E28EFF` `#FF71A4` `#FFA841` `#FFC236` | 검색바 테두리 · 배지 · 배경 워시 |
| Blue | `#1E84FF` | 로고 마크 베이스 · 액션 버튼 |
| OK | `#1EA672` | 조건 확인됨 |
| Hold | `#B08900` | 조건 보류 |
| Ink | `#16181D` | 본문 텍스트 |

### 워드마크 (글자별 컬러)

| 글자 | HEX |
|---|---|
| **D** | `#1E84FF` |
| **a** | `#18BB68` |
| **u** | `#FDA313` |
| **m** | `#F95F65` |

### 시안 반영 방식

- **로고 마크** — 시안의 "D" 패스와 5겹 radial gradient를 그대로 옮겨 `<symbol id="sym-mark">`으로 정의했습니다. `viewBox`를 원본 좌표계(`198.78 800 23.863 24`)로 유지했기 때문에 `userSpaceOnUse` 그라디언트가 크기와 무관하게 정확히 재현됩니다.
- **그라디언트 테두리** — 시안의 이중 rect 구조(바깥 그라디언트 rect / 안쪽 흰 rect)를 `padding` + 중첩 배경으로 구현했습니다. 홈 1.5px, 인트로 2.25px.
- **인트로 배경** — 시안의 `fill-opacity: 0.1` 그라디언트 오버레이를 의사요소(`::before`)로 재현했습니다.
- **마이크 아이콘** — 이모지를 걷어내고 시안의 벡터 패스(검정 64%)로 교체했습니다.

### ⚠️ 워드마크 교체 안내

현재 "Daum" 워드마크는 **텍스트로 조판**돼 있습니다.
공식 워드마크 이미지를 쓰려면:

1. 이미지를 `assets/daum-wordmark.png` 로 저장
2. `index.html`에서 아래 부분의 주석을 풀고 `<div>` 줄을 삭제

```html
<!-- <img class="home-wordmark" src="assets/daum-wordmark.png" alt="Daum" /> -->
<div class="home-wordmark wordmark">Daum</div>
```

검색 결과 화면 헤더의 `.hdr-wordmark`도 동일하게 교체하면 됩니다.

---

## GitHub Pages 배포

### 1. 레포 생성 후 푸시

```bash
cd ~/Desktop/코로라
git init
git add .
git commit -m "쇼핑, 다음을 이어주는 검색 — 프로토타입"
git branch -M main
git remote add origin https://github.com/ellyko512-oss/kosearch.git
git push -u origin main
```

### 2. Pages 켜기

GitHub 레포 페이지 → **Settings** → 왼쪽 **Pages** →
**Source: Deploy from a branch** / **Branch: `main`** / **Folder: `/ (root)`** → **Save**

1–2분 뒤 아래 주소로 접속됩니다. 이 링크를 과제로 제출하면 됩니다.

```
https://ellyko512-oss.github.io/kosearch/
```

> 레포는 **Public**이어야 무료 플랜에서 Pages가 동작합니다.

### 로컬에서 먼저 확인하기

```bash
cd ~/Desktop/코로라 && python3 -m http.server 4321
```

브라우저에서 `http://localhost:4321` 로 접속합니다.

---

## 기술 노트

- 화면 전환은 DOM 교체 없이 `.page.active` 클래스 토글 + `opacity`/`transform`으로만 처리합니다.
- 로컬 섹션은 `max-height` 트랜지션으로 펼쳐지며, 구매 완료 전에는 DOM에 존재하되 접근 불가 상태입니다.
- 폰 프레임은 390 × 844 (iPhone 기준), 뷰포트 높이에 따라 축소됩니다.
- 화면 폭 900px 미만에서는 프로토타입과 설명 패널이 1단으로 쌓입니다.

---

## 고지

학습·포트폴리오 목적의 **디자인 컨셉 시안**입니다.
Daum은 Kakao Corp.의 상표이며, 본 결과물은 실제 서비스와 무관한 비공식 제안물입니다.
상품명·가격·후기 수치는 모두 예시용 더미 데이터입니다.

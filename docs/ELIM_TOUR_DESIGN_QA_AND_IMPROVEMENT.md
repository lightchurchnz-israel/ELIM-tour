# ELIM TOUR — 디자인 검수 및 개선 작업 지침

> 대상 저장소: `lightchurchnz-israel/ELIM-tour`  
> 작업 브랜치: `feature/redesign-v1`  
> Draft PR: `#1`  
> 현재 Preview deployment: `https://elim-tour-nz-english-jjcnn89oj-nz-gst-saas.vercel.app`  
> 작성일: 2026-09-07

이 문서는 현재 ELIM TOUR 리디자인을 **실제 눈으로 검수하고, 디자인 품질과 예약 전환을 한 단계 더 개선하기 위한 실행용 QA 문서**다. 단순한 의견 목록이 아니라, Codex 또는 개발자가 이 문서만 읽고도 순서대로 점검하고 수정할 수 있도록 작성한다.

---

## 1. 이번 작업의 최종 목표

사이트는 다음과 같이 보여야 한다.

> **뉴질랜드 현지의 믿을 수 있는 소규모 프라이빗 투어 회사. 조용하고 현대적이며, 사진과 정보가 주인공이고, 디자인이 과하게 자신을 드러내지 않는 사이트.**

다음 느낌은 피한다.

- AI가 자동 생성한 랜딩페이지 느낌
- 스타트업 SaaS 템플릿 느낌
- 과장된 럭셔리 호텔 사이트 느낌
- 패키지 여행 전단지 느낌
- 지나치게 장식적인 브랜딩

디자인의 우선순위는 다음과 같다.

1. 신뢰
2. 상품 이해
3. 예약하기 쉬움
4. 사진의 아름다움
5. 브랜드 일관성
6. 장식

장식은 항상 마지막이다.

---

# 2. 사용자 확정 디자인 원칙 — 변경 금지

아래 원칙은 취향 제안이 아니라 **확정 요구사항**이다.

## 2.1 폰트

- **명조/세리프 사용 금지**
- Georgia 금지
- Times / Times New Roman 금지
- `serif` fallback 금지
- **기울임/italic 사용 금지**
- 제목 강조를 위해 `<em>` 또는 `<i>`를 사용하지 않는다.
- 한국어와 영어 모두 산세리프 계열을 사용한다.

권장 기본 스택:

```css
font-family:
  Inter,
  -apple-system,
  BlinkMacSystemFont,
  "Segoe UI",
  "Apple SD Gothic Neo",
  "Noto Sans KR",
  Arial,
  sans-serif;
```

정적 검사:

```bash
grep -RniE 'Georgia|Times New Roman|Times,|font-family:[^;]*serif|font-style:[[:space:]]*italic|<em[ >]|<i[ >]' . \
  --exclude-dir=.git \
  --exclude='*.md'
```

**결과 0건이 목표다.**

## 2.2 AI스럽게 보이는 디자인 요소 제거

다음 요소를 새로 추가하지 않는다.

- 과도한 pill 버튼
- 모든 카드에 큰 둥근 모서리
- 모든 섹션을 카드 안에 또 넣는 구성
- 유리/글래스모피즘
- `backdrop-filter: blur(...)` 중심 디자인
- 과한 그림자
- 떠 있는 반투명 박스
- 그라데이션으로 채운 카드
- 의미 없는 원형 아이콘
- 번호 `01 02 03 04`를 장식처럼 반복하는 패턴
- decorative blob / abstract gradient sphere
- 필요 없는 마이크로 애니메이션
- 과장된 스크롤 애니메이션
- 큰 따옴표 아이콘을 장식으로 쓰는 후기 카드
- 비행기/지도핀/지구본 같은 상투적 여행 아이콘 남발
- 섹션마다 `eyebrow + 거대한 문구 + 설명 + 카드 3개` 패턴을 기계적으로 반복

### 허용되는 시각 요소

- 좋은 여행 사진
- 충분한 흰 여백
- 얇은 divider
- 명확한 타이포 위계
- 짙은 포레스트 그린
- 따뜻한 아이보리/화이트
- 아주 제한적인 브랜드 포인트 색
- 3–5px 정도의 작은 radius
- 기능적 hover/focus 상태

---

# 3. 로고 — 반드시 유지

현재 복구된 **산 + 물결 + 해 + ELIM TOUR 로고**가 브랜드 로고다.

파일:

- `assets/logo-horizontal.svg`
- `assets/logo-white.svg`

## 로고 규칙

- 로고 심볼을 제거하지 않는다.
- 단순 텍스트 `ELIM TOUR`로 대체하지 않는다.
- AI 디자인 요소를 제거한다는 이유로 브랜드 로고를 삭제하지 않는다.
- 헤더 배경에 따라 white/dark 버전을 사용한다.
- SVG 내부 글자는 가능한 한 폰트 `<text>`에 의존하지 않고 vector path를 유지한다.
- 스크롤 전/후 모두 로고가 확실히 보여야 한다.

## 로고 시각 검수

Desktop 1440px:

- [ ] 첫 화면에서 로고가 즉시 인식된다.
- [ ] 로고가 너무 작지 않다.
- [ ] 네비게이션과 시각적 무게가 균형을 이룬다.
- [ ] 산/물결/해 심볼이 뭉개지지 않는다.
- [ ] `ELIM` 글자가 한눈에 읽힌다.
- [ ] `TOUR`가 너무 작아서 사라지지 않는다.

Mobile 390px:

- [ ] 로고가 햄버거/Menu와 겹치지 않는다.
- [ ] 최소 135–150px 정도의 실질적인 가독성을 확보한다.
- [ ] 화면 좌우 여백이 지나치게 좁지 않다.

스크롤 후 white header:

- [ ] dark logo로 자연스럽게 전환된다.
- [ ] logo flash / disappearance가 없다.
- [ ] header 높이가 줄어도 logo가 눌리거나 잘리지 않는다.

---

# 4. 검수 환경

최소 다음 viewport에서 실제 브라우저로 확인한다.

| 구분 | Viewport |
|---|---:|
| Desktop large | 1440 × 900 |
| Desktop common | 1280 × 800 |
| Tablet | 1024 × 768 |
| iPhone | 390 × 844 |
| Small mobile | 375 × 812 |

Safari 계열 검수가 중요하다.

- iPhone Safari
- macOS Safari
- Chrome desktop

## Preview 접근 관련 주의

Vercel Preview는 조직 인증 정책 때문에 자동화 브라우저나 외부 환경에서 `Your organization doesn't allow you to view this site`로 차단될 수 있다.

따라서:

1. Vercel에 로그인된 실제 브라우저에서 확인하거나
2. Vercel의 temporary share URL을 새로 발급한 후 확인한다.

**페이지를 실제로 보지 못했는데 디자인 검수를 완료했다고 기록하지 않는다.**

---

# 5. 작업 시작 전 Screenshot Baseline

수정 전에 반드시 다음 이미지를 저장한다.

```text
docs/qa/before/
  desktop-home-1440.png
  desktop-tours-1440.png
  desktop-tour-modal-1440.png
  mobile-home-390.png
  mobile-tours-390.png
  mobile-tour-modal-390.png
  korean-home-390.png
  korean-tour-modal-390.png
```

수정 후 동일한 파일명을 `docs/qa/after/`에 저장한다.

가능하면 **같은 viewport, 같은 scroll 위치**에서 캡처한다.

---

# 6. 현재 소스에서 이미 확인된 사항

다음은 시각 추측이 아니라 현재 `feature/redesign-v1` 소스 구조에서 확인 가능한 내용이다.

## 이미 반영된 좋은 방향

- 전체 기본 폰트가 산세리프 스택이다.
- `em, i { font-style: normal; }` 처리가 있다.
- 버튼 radius가 비교적 작다.
- 투어 카드가 과도한 그림자 카드가 아니라 사진 + 텍스트 중심이다.
- trust 영역이 단순 divider 구조다.
- story 영역도 카드 덩어리보다 grid/divider 구조다.
- 헤더가 hero 위에서는 투명, 스크롤 후에는 흰색으로 바뀐다.
- 투어 type `PRIVATE` / `SMALL GROUP`이 분리된다.
- 영문/한글을 같은 데이터와 UI 구조로 처리한다.
- Bókun 예약 iframe을 상품별로 연결한다.
- 구형 Golf/Sunset slug redirect가 존재한다.
- contact form이 가짜 성공 메시지를 보여주지 않고 mailto 방식임을 명시한다.

## 아직 반드시 눈으로 확인해야 하는 부분

- 실제 hero 사진 위에서 흰색 로고가 충분히 보이는가
- hero 제목 크기가 과하지 않은가
- 4개의 review가 desktop에서 너무 촘촘하지 않은가
- 3-column tour grid가 사진과 설명을 답답하게 만들지 않는가
- modal 내 gallery와 Bókun iframe 비율이 자연스러운가
- 한글 제목 줄바꿈이 어색하지 않은가
- 모바일에서 sticky booking button이 콘텐츠를 가리지 않는가

---

# 7. P0 — 가장 먼저 검수하고 고칠 것

## 7.1 로고 visibility

가장 먼저 확인한다.

현재 header는:

- 첫 화면: `logo-white.svg`
- scroll 후: `logo-horizontal.svg`

을 사용한다.

### 완료 기준

- [ ] 첫 로딩 즉시 로고 표시
- [ ] 이미지 로딩 지연으로 빈 자리가 보이지 않음
- [ ] scroll 0 → 100px 사이 전환 중 사라지지 않음
- [ ] browser cache refresh 후에도 정상
- [ ] iPhone Safari 정상
- [ ] footer logo 정상

필요하면 로고에 명시적인 width/height를 지정해 CLS를 막는다.

## 7.2 Hero headline 크기

현재 CSS의 최대 hero title은 상당히 크다.

```css
font-size: clamp(3.3rem, 6.6vw, 6.4rem);
```

크다고 무조건 나쁜 것은 아니지만, 이번 브랜드 목표는 **조용한 신뢰감**이다.

### 눈으로 판단할 것

- 화면의 절반 이상을 제목이 차지하면 줄인다.
- 사진의 중요한 피사체를 제목이 압도하면 줄인다.
- `New Zealand, at your pace.`가 광고 카피보다 브랜드 문장처럼 보이게 한다.

### 1차 권장 범위

Desktop:

```css
font-size: clamp(3.2rem, 5.2vw, 5.3rem);
line-height: .98;
```

Mobile:

```css
font-size: 2.8rem ~ 3.2rem;
line-height: 1.0;
```

**실제 스크린샷을 보고 최종 결정한다.**

## 7.3 Hero overlay

현재 overlay는 왼쪽이 꽤 어둡고 오른쪽이 밝아지는 구조다.

목표:

- 텍스트가 읽히는 정도까지만 어둡게 한다.
- 사진이 탁하거나 회색으로 죽지 않게 한다.
- 전체 사진 위에 무거운 검정 필터를 씌운 느낌을 피한다.

완료 기준:

- [ ] 흰 글자 대비 충분
- [ ] 사진의 실제 색감 유지
- [ ] 포레스트 그린 색조가 과하게 덮이지 않음
- [ ] 모바일에서도 피사체와 텍스트 충돌 없음

## 7.4 Header

검수 항목:

- [ ] desktop에서 menu 간격이 지나치게 넓지 않음
- [ ] `Book now`가 다른 메뉴보다 명확하지만 공격적으로 보이지 않음
- [ ] EN / 한글 전환이 별도의 pill UI처럼 보이지 않음
- [ ] header height 70–82px가 로고에 적합함
- [ ] scroll header가 불필요한 blur/glass 느낌이 없음
- [ ] mobile Menu 버튼이 텍스트 또는 단순 아이콘 중 하나로 일관됨

권장:

- header background는 opaque 또는 97–100% white
- blur 사용 금지
- border-bottom 1px 정도만 사용

---

# 8. P1 — Tours 영역 개선

투어 상품이 사이트의 주인공이다.

## 8.1 카드 구성

현재 구조는 좋은 출발점이다.

권장 정보 순서:

1. 사진
2. PRIVATE / SMALL GROUP
3. duration + group size
4. 상품명
5. 2줄 요약
6. `View tour` / `Book now`

### 체크

- [ ] 모든 카드 이미지 비율 동일
- [ ] 제목 높이가 달라도 CTA baseline이 너무 흐트러지지 않음
- [ ] 요약은 최대 2–3줄
- [ ] badge가 사진을 과하게 가리지 않음
- [ ] `View tour`와 `Book now`의 역할 차이가 명확함
- [ ] hover가 없어도 카드가 클릭 가능하다는 것이 이해됨

## 8.2 hover 효과

현재 이미지에 작은 zoom effect가 있다.

```css
transform: scale(1.015)
```

이번 디자인 목표에서는 이것도 반드시 필요하지 않다.

**눈으로 봤을 때 템플릿 느낌이 나면 제거한다.**

권장:

- 이미지 zoom 제거
- 텍스트 링크의 underline / opacity 정도만 사용

## 8.3 Filter

현재 underline 방식은 유지할 수 있다.

체크:

- [ ] pill button으로 바꾸지 않는다.
- [ ] mobile horizontal scroll이 자연스럽다.
- [ ] 첫/마지막 filter가 화면 밖에서 잘리지 않는다.
- [ ] 선택 상태가 색 + underline으로 명확하다.

---

# 9. P1 — Reviews 영역 개선

현재 desktop에서 4개 review를 4 columns로 동시에 보여준다.

이 구조는 화면 폭에 따라 **너무 촘촘하고 텍스트가 잘게 보일 가능성**이 있다.

## 우선 검토안

### Option A — 2 × 2 grid

가장 추천한다.

- 읽기 편함
- 후기 한 개의 무게감 증가
- 과도한 카드 느낌 없이 divider로 유지 가능

### Option B — 3 columns + 1개 다음 줄

비추천. 균형이 나쁘다.

### Option C — 4 columns 유지

1440px에서 실제로 충분히 넓고 읽기 좋을 때만 유지한다.

## Reviews 디자인 규칙

- 별 5개 emoji/icon 남발 금지
- 현재처럼 `5.0 / 5` 텍스트 방식 가능
- quote 자체가 주인공
- 큰 따옴표 장식 금지
- 고객명/국가/상품명은 작고 차분하게
- 각 review 카드에 그림자와 둥근 박스를 추가하지 않는다.

완료 기준:

- [ ] 본문 font 14px 이하로 지나치게 작지 않음
- [ ] 한 review당 line length 적당함
- [ ] mobile에서 1 column
- [ ] Tripadvisor 원문 링크 명확

---

# 10. P1 — About / ELIM Story

현재 2-column 구조는 유지할 수 있다.

## 개선 방향

`ELIM = oasis / 쉼` 스토리를 너무 추상적으로 늘리지 않는다.

한 문단으로 충분하다.

오른쪽 4개 가치도 다음 정도만 유지한다.

- Small by design
- Your pace matters
- Warm local care
- Secure booking

### 체크

- [ ] 4개 항목에 불필요한 아이콘 없음
- [ ] 번호 장식 없음
- [ ] divider만 사용
- [ ] 한 항목 설명 2줄 안팎
- [ ] 실제 서비스와 직접 관계없는 마케팅 수식어 제거

---

# 11. P1 — Tour Detail Modal / Booking

이 부분은 디자인보다 **예약 전환**이 중요하다.

## Desktop

구성:

- gallery
- 왼쪽: tour info
- 오른쪽: Bókun booking

### 체크

- [ ] modal width가 화면에 비해 너무 크지 않음
- [ ] close 버튼이 즉시 보임
- [ ] close가 둥근 floating icon처럼 보이지 않음
- [ ] gallery가 상품 설명보다 과도하게 높지 않음
- [ ] booking iframe 첫 화면에서 의미 있는 날짜/예약 UI가 보임
- [ ] iframe 내부의 이중 스크롤이 심하지 않음
- [ ] booking card border가 단정함

## Mobile

모바일에서는 modal보다 full-page detail처럼 느껴져야 한다.

- [ ] close가 항상 접근 가능
- [ ] gallery → 핵심 정보 → 상세 → 예약 순서가 이해됨
- [ ] sticky `Check dates & book`가 브라우저 하단 UI와 겹치지 않음
- [ ] iPhone safe-area 고려
- [ ] CTA가 본문 마지막 줄을 가리지 않음
- [ ] iframe 가로 overflow 없음

권장:

```css
.mobile-book {
  padding-bottom: env(safe-area-inset-bottom);
}
```

또는 safe-area를 포함한 wrapper 사용.

---

# 12. P1 — 한글 UI 검수

영문 화면이 좋다고 한글 화면도 자동으로 좋은 것은 아니다.

## 반드시 별도로 확인

- [ ] 한국어 제목에 부자연스러운 단어 단위 줄바꿈 없음
- [ ] `letter-spacing`이 한글에 과하게 적용되지 않음
- [ ] uppercase 스타일을 한글에 억지로 적용하지 않음
- [ ] 700 이상 bold가 지나치게 두껍지 않음
- [ ] 작은 label이 너무 작아지지 않음
- [ ] `[단독]` 표기와 UI badge가 중복되어 지저분해 보이지 않음
- [ ] `단독 프라이빗` 표현이 필요한 곳에서만 사용됨
- [ ] 모바일에서 상품명 3–4줄 이상 늘어지지 않음

## 한국어 폰트

가능하면:

```css
"Apple SD Gothic Neo", "Noto Sans KR", sans-serif
```

을 자연스럽게 사용한다.

명조체는 사용하지 않는다.

---

# 13. P1 — Contact 영역

현재 form은 실제 서버 제출이 아니라 **메일 앱을 여는 방식**이다.

이것은 가짜 성공 메시지보다 낫지만 최종 상태로는 개선 여지가 있다.

## 디자인 검수

- [ ] form이 SaaS signup form처럼 보이지 않음
- [ ] input radius 0–4px 정도
- [ ] label 명확
- [ ] focus state 충분
- [ ] textarea 높이 적절
- [ ] button 하나만 강조

## 기능 개선 권장

다음 단계에서는 실제 제출 API 구현을 고려한다.

예:

- Vercel Function / Route
- Resend 등 검증된 email provider
- 기존 운영 이메일 workflow

실제 전송 구현 전까지는 현재처럼 **mailto임을 솔직하게 표시**한다.

---

# 14. P1 — Footer

Footer는 조용해야 한다.

- 로고
- 짧은 브랜드 문장
- Tours / Reviews / FAQ
- 이메일 / 전화
- Terms / Privacy

이면 충분하다.

체크:

- [ ] 로고가 너무 크지 않음
- [ ] footer 안에서 다시 마케팅 섹션을 만들지 않음
- [ ] SNS icon이 실제 계정 없는데 placeholder로 나오지 않음
- [ ] email / phone이 클릭 가능
- [ ] legal link 실제 페이지 연결

---

# 15. Bókun 예약 데이터 — 절대 regression 금지

현재 canonical source:

`data/tours.json`

현재 한글 사이트에서 검증된 9개 상품 mapping을 유지해야 한다.

| slug | productId |
|---|---:|
| `full-day-auckland-discovery` | `949491` |
| `auckland-half-day-city-tour-heart-of-auckland` | `890706` |
| `private-cathedral-cove-and-hot-water-beach` | `961235` |
| `private-auckland-half-day-city-tour` | `1081437` |
| `private-bay-of-islands-discovery-from-auckland` | `955749` |
| `private-auckland-signature-golf` | `1135444` |
| `private-haka-to-waves-auckland-west-coast-journey` | `949492` |
| `private-sunset-wine-hot-pool-dinner` | `945266` |
| `private-airport-transfer-from-to-auckland-airport` | `1066960` |

### QA

- [ ] 9개 모두 존재
- [ ] productId 변경 없음
- [ ] booking URL의 마지막 ID와 productId 일치
- [ ] 각 `Book now`가 자기 상품 iframe을 연다.
- [ ] 한글/영문 모두 같은 mapping 사용

---

# 16. Routing / 링크 QA

반드시 테스트:

- `/`
- `/tours`
- `/ko`
- 9개 `/tour/:slug`
- `/terms`
- `/privacy`

Legacy redirect:

- `/tour/private-auckland-signature-golf-experience`
  → `/tour/private-auckland-signature-golf`
- `/tour/private-auckland-sunset-wine-hot-pool-dinner`
  → `/tour/private-sunset-wine-hot-pool-dinner`

완료 기준:

- [ ] internal 404 없음
- [ ] 메뉴 클릭 정상
- [ ] logo 클릭 → home
- [ ] language switch 후 현재 UI가 깨지지 않음
- [ ] browser back/forward에서 modal route 정상
- [ ] direct detail URL 진입 정상

---

# 17. Accessibility QA

필수:

- [ ] 모든 interactive item keyboard 접근 가능
- [ ] visible focus 있음
- [ ] modal focus trap 정상
- [ ] ESC close
- [ ] modal close button label 명확
- [ ] 이미지 alt text 존재
- [ ] heading 순서 h1 → h2 → h3
- [ ] form label 연결
- [ ] 색만으로 상태를 표현하지 않음
- [ ] 최소 contrast AA
- [ ] touch target 최소 약 44px
- [ ] prefers-reduced-motion 고려

---

# 18. Performance QA

확인:

- [ ] hero image 과도한 원본 사이즈 로드 안 함
- [ ] tour images lazy loading
- [ ] gallery 첫 이미지 외 lazy loading
- [ ] image aspect-ratio 또는 width/height 확보
- [ ] logo CLS 없음
- [ ] 불필요한 JavaScript animation 없음
- [ ] console error 없음
- [ ] network 404 없음

Lighthouse 목표:

- Performance: 85+
- Accessibility: 95+
- Best Practices: 95+
- SEO: 95+

점수를 만들기 위해 디자인/기능을 망치지 않는다.

---

# 19. 디자인 세부 수치 가이드

정답이 아니라 **시작점**으로 사용한다.

## Radius

- Button: 3–5px
- Input: 2–4px
- Card: 기본 0px
- Modal: 0–4px

## Shadow

기본적으로 사용하지 않는다.

필요한 경우 modal overlay에서만 아주 약하게 사용한다.

## Section vertical spacing

Desktop:

- 88–104px

Mobile:

- 64–76px

한 화면에서 빈 공간이 지나치게 커 보이면 무조건 100px을 고집하지 않는다.

## Content width

- 전체 max: 약 1200–1240px
- 긴 본문 line length: 600–720px

---

# 20. “AI 느낌” 최종 육안 테스트

화면을 캡처한 다음 아래 질문에 답한다.

### 질문 1

**이 사이트에서 사진을 빼면 AI 랜딩페이지 템플릿처럼 보이는가?**

그렇다면:

- 카드 수를 줄인다.
- 큰 문구 반복을 줄인다.
- 장식 요소를 없앤다.
- 섹션 패턴을 단순화한다.

### 질문 2

**모든 섹션이 같은 템플릿 패턴으로 반복되는가?**

예:

`작은 영어 label → 큰 제목 → 설명 → 3~4개 카드`

이 패턴이 연속 3회 이상이면 구조를 바꾼다.

### 질문 3

**디자인보다 투어 사진과 상품이 먼저 보이는가?**

아니면 디자인을 더 줄인다.

### 질문 4

**5초 안에 아래 세 가지가 보이는가?**

1. 뉴질랜드/오클랜드 투어 회사
2. Private / small group
3. 예약할 수 있음

아니면 첫 화면을 수정한다.

### 질문 5

**고객이 “작은 실제 여행사”라는 신뢰를 느끼는가?**

과도하게 매끈한 template 느낌보다 실제 운영자/현지 전문성이 느껴져야 한다.

---

# 21. 개선 우선순위

## P0 — Merge 전에 반드시

- [ ] 로고 desktop/mobile/scroll visibility 완전 해결
- [ ] hero typography 육안 검수
- [ ] hero overlay 육안 검수
- [ ] 9개 Bókun booking 회귀 테스트
- [ ] 모든 menu / route 테스트
- [ ] tour modal 모바일 테스트
- [ ] 한글 모바일 테스트
- [ ] console/network error 제거

## P1 — 디자인 품질

- [ ] Reviews 4-column vs 2×2 실제 비교 후 결정
- [ ] tour card hover zoom 필요 여부 결정
- [ ] section spacing 다듬기
- [ ] header menu spacing 다듬기
- [ ] contact form 밀도 조정
- [ ] footer 여백/로고 크기 조정

## P2 — 후속 기능

- [ ] 실제 inquiry backend 구현
- [ ] 상품 가격/availability의 더 자연스러운 Bókun integration 검토
- [ ] real production analytics/SEO 검수
- [ ] `/ko` 구조를 장기적으로 production 도메인에 통합할지 결정

---

# 22. 완료 후 필요한 결과물

작업자는 PR에 다음을 남긴다.

## Screenshots

- Desktop Home before / after
- Mobile Home before / after
- Desktop Tours before / after
- Mobile Tours before / after
- Desktop Tour Modal after
- Mobile Tour Modal after
- Korean Home mobile after
- Korean Tour Modal mobile after

## QA 결과

```text
Design QA: PASS / FAIL
Logo QA: PASS / FAIL
Booking QA 9/9: PASS / FAIL
Routes QA: PASS / FAIL
Mobile QA: PASS / FAIL
Korean QA: PASS / FAIL
Accessibility basic QA: PASS / FAIL
Console errors: 0 / N
Broken links: 0 / N
```

실패 항목은 숨기지 말고 정확히 기록한다.

---

# 23. Codex 실행 지시문

아래 문장을 그대로 Codex에 사용할 수 있다.

> `feature/redesign-v1` 브랜치에서 작업하라. 먼저 `docs/ELIM_TOUR_DESIGN_QA_AND_IMPROVEMENT.md`와 기존 redesign brief를 전부 읽어라. 현재 Vercel Preview를 desktop 1440×900, mobile 390×844에서 실제로 열고 before screenshot을 저장한 뒤 검수를 시작하라. 사용자가 확정한 규칙은 (1) 명조/serif 전면 금지, (2) italic 전면 금지, (3) AI 생성 랜딩페이지처럼 보이는 장식 요소 제거, (4) 현재 산+물결+해 ELIM TOUR 로고 유지다. 디자인을 바꾸면서 9개 Bókun productId와 booking URL은 절대 변경하지 마라. 가장 먼저 logo visibility, hero typography, review density, tour cards, mobile tour modal, Korean mobile layout을 눈으로 검수하고 수정하라. 작업 후 동일 viewport의 after screenshot을 남기고 모든 메뉴, 9개 booking, legacy redirect, language switch를 클릭 테스트하라. 테스트 결과를 PR comment에 PASS/FAIL 형태로 기록하라. 실제 화면을 열지 못했으면 디자인 QA를 PASS로 기록하지 마라.`

---

# 24. 최종 승인 기준

최종 화면을 봤을 때 다음 문장이 모두 맞아야 한다.

- **깔끔하다.**
- **글씨가 현대적이다.**
- **로고가 분명히 보인다.**
- **여행 사진이 주인공이다.**
- **어디를 눌러 예약하는지 바로 안다.**
- **Private와 Small Group의 차이가 바로 보인다.**
- **한글 화면도 번역판처럼 어색하지 않다.**
- **AI가 만든 템플릿 같지 않다.**
- **실제 뉴질랜드 현지 여행사처럼 믿음이 간다.**

이 기준을 만족하기 전에는 `main`으로 merge하지 않는다.

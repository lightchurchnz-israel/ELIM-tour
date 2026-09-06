# NZ ELIM TOUR — Codex Website Redesign & Repair Brief

> 작성 기준: 2026-09-07 (NZST)
>
> 목적: 현재 ELIM TOUR 영문/한글 사이트의 **예약 링크 안정성, 브랜드 일관성, 프리미엄 디자인, 모바일 UX, 신뢰 요소, SEO, 실제 문의 전환**을 한 번에 개선한다.  
> 이 문서는 Codex가 저장소를 열고 바로 작업을 시작할 수 있는 **구현 명세서 + QA 체크리스트**다.

---

## 0. 가장 중요한 전제

### 사이트

- 영문 운영 사이트: `https://www.nzelimtour.com/`
- 한글 사이트: `https://elim-tour-nz-korean.vercel.app/`

### 핵심 판단

1. **한글 사이트의 투어/예약 링크 구조는 정상이다.**
   - `data/tours.json`에서 9개 상품을 관리한다.
   - 각 상품은 정확한 `productId`와 `bokun_booking_url`을 갖고 있다.
   - 카드의 `상세 정보` / `예약하기` 버튼은 `openTourModal(slug)`를 열고, 상세 모달 오른쪽에 해당 상품의 Bókun 예약 iframe을 삽입한다.
   - 이 구조와 Bókun 매핑을 **절대로 깨뜨리지 말 것**.

2. 영문 사이트는 일부 홈 카드 URL이 오래된 slug를 사용해 404가 발생하고, `/tours`와 홈의 상품 구성이 서로 다르다.

3. 영문 상세 페이지 중 일부는 구형 Bókun 템플릿/헤더/푸터가 남아 있어 사이트 전체가 하나의 브랜드처럼 보이지 않는다.

4. 한글 사이트는 투어 링크는 정상이나, 별도로 확인된 기능성 이슈가 있다.
   - 문의 폼은 현재 실제 전송 없이 `alert()`만 띄우고 reset한다.
   - Terms & Conditions / Privacy Policy 링크가 실제 문서가 아니라 `#contact`로 연결된 placeholder다.
   - 이 부분은 투어 예약 링크와 별개의 문제다.

---

# 1. 작업 시작 전 Codex가 먼저 해야 할 일

코드를 수정하기 전에 반드시 다음을 수행한다.

1. 현재 repo 구조를 파악한다.
   - 영문 `nzelimtour.com` 소스가 어디에 있는지 확인
   - 한글 Vercel 사이트 소스가 같은 repo인지 별도 repo인지 확인
   - Bókun으로부터 자동 생성되는 영역과 직접 관리하는 영역을 구분

2. 변경 전 상태를 보존한다.
   - 현재 production branch/commit 기록
   - 가능하면 `docs/redesign/before/`에 baseline screenshot 저장

3. 실제 브라우저로 아래 화면을 모두 확인한다.
   - Desktop: 1440×900
   - Mobile: 390×844 또는 유사 iPhone viewport
   - Home / Tours / 각 투어 상세 / Reviews / FAQ / Contact / Korean site

4. 변경 전 자동 링크 검사를 돌린다.
   - 내부 링크 404 검사
   - 9개 Bókun booking URL 상태 검사
   - 외부 Tripadvisor 링크 검사

5. 작업 후 동일한 검사를 다시 실행하고 QA 결과를 문서화한다.

---

# 2. 현재 상태 스크린샷 레퍼런스

아래 이미지는 **현재 배포 사이트를 실시간 캡처하는 외부 screenshot endpoint**다. 문서를 나중에 열면 사이트 변경 사항이 반영될 수 있으므로, Codex는 작업 시작 즉시 Playwright/Chrome으로 로컬 PNG baseline을 별도로 저장한다.

## 2.1 영문 홈 — 현재

![English home current](https://image.thum.io/get/width/1200/crop/1200/noanimate/https://www.nzelimtour.com/)

## 2.2 영문 `/tours` — 현재

![English tours current](https://image.thum.io/get/width/1200/crop/1200/noanimate/https://www.nzelimtour.com/tours)

## 2.3 영문 홈의 현재 깨진 Golf 링크

현재 홈 카드가 아래 **오래된 slug**로 연결되어 404가 발생한다.

`/tour/private-auckland-signature-golf-experience`

![Broken Golf route](https://image.thum.io/get/width/1200/crop/1200/noanimate/https://www.nzelimtour.com/tour/private-auckland-signature-golf-experience)

정상 경로는 다음이다.

`/tour/private-auckland-signature-golf`

![Correct Golf route](https://image.thum.io/get/width/1200/crop/1200/noanimate/https://www.nzelimtour.com/tour/private-auckland-signature-golf)

## 2.4 영문 홈의 현재 깨진 Sunset 링크

현재 홈 카드의 오래된 경로:

`/tour/private-auckland-sunset-wine-hot-pool-dinner`

정상 경로:

`/tour/private-sunset-wine-hot-pool-dinner`

![Correct Sunset route](https://image.thum.io/get/width/1200/crop/1200/noanimate/https://www.nzelimtour.com/tour/private-sunset-wine-hot-pool-dinner)

## 2.5 한글 사이트 — 현재

![Korean site current](https://image.thum.io/get/width/1200/crop/1200/noanimate/https://elim-tour-nz-korean.vercel.app/)

한글 사이트의 **투어 카드 → 상세 모달 → Bókun 예약 iframe** 구조는 기능적으로 더 나은 기준점이다. 영문 사이트를 고칠 때 이 구조와 product mapping을 참고한다.

---

# 3. 한글 사이트의 정상 Bókun 매핑 — Source of Truth

아래 9개는 현재 한글 사이트 `data/tours.json`에서 확인된 canonical mapping이다.

| 상품 | slug | Bókun Product ID | Booking URL |
|---|---|---:|---|
| Full-Day Auckland Discovery | `full-day-auckland-discovery` | `949491` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/949491` |
| Auckland Half Day City Tour - Heart of Auckland | `auckland-half-day-city-tour-heart-of-auckland` | `890706` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/890706` |
| [PRIVATE] Cathedral Cove and Hot Water Beach | `private-cathedral-cove-and-hot-water-beach` | `961235` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/961235` |
| [PRIVATE] Auckland Half Day City Tour | `private-auckland-half-day-city-tour` | `1081437` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/1081437` |
| [PRIVATE] Bay of Islands Discovery From Auckland | `private-bay-of-islands-discovery-from-auckland` | `955749` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/955749` |
| [PRIVATE] Auckland Signature Golf | `private-auckland-signature-golf` | `1135444` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/1135444` |
| [PRIVATE] Haka to Waves Auckland West Coast Journey | `private-haka-to-waves-auckland-west-coast-journey` | `949492` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/949492` |
| [PRIVATE] SUNSET. Wine. Hot Pool. Dinner | `private-sunset-wine-hot-pool-dinner` | `945266` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/945266` |
| [PRIVATE] Airport Transfer from/to Auckland Airport | `private-airport-transfer-from-to-auckland-airport` | `1066960` | `https://widgets.bokun.io/online-sales/8c6bd3e1-0496-4123-a2ea-f0d6919b13af/experience-calendar/1066960` |

### 절대 규칙

- 위 `productId`와 `bokun_booking_url`을 임의로 변경하지 않는다.
- Bókun에서 상품 ID가 실제로 변경되었다는 근거가 있을 때만 갱신한다.
- 영어/한국어 상품 데이터는 서로 다른 하드코딩 목록을 만들지 말고 가능한 한 **한 개의 canonical product model**을 공유한다.

---

# 4. P0 — 예약 손실/깨진 링크부터 수정

## 4.1 영문 홈의 Golf 카드 404 수정

### 현재 잘못된 URL

`/tour/private-auckland-signature-golf-experience`

### 올바른 URL

`/tour/private-auckland-signature-golf`

### 추가 조치

- 기존 오래된 slug에는 301 redirect를 추가한다.
- 외부 검색결과/기존 공유 링크가 깨지지 않도록 한다.

---

## 4.2 영문 홈의 Sunset 카드 404 수정

### 현재 잘못된 URL

`/tour/private-auckland-sunset-wine-hot-pool-dinner`

### 올바른 URL

`/tour/private-sunset-wine-hot-pool-dinner`

### 추가 조치

- 오래된 slug → 새 slug 301 redirect.

---

## 4.3 모든 영문 상품 카드 URL을 한글 사이트 canonical slug와 대조

홈의 9개 카드와 `/tours` 목록이 서로 따로 관리되는 것으로 보인다.

### 목표

- 카드 제목
- slug
- Bókun productId
- duration
- tour type
- summary
- image

을 한 곳에서 관리한다.

가능하면 `tours.json` 또는 이에 준하는 단일 데이터 소스를 사용한다.

---

## 4.4 영문 상세 페이지의 상단 `BOOK NOW` 동작 수정

현재 일부 상품 상세에서 `BOOK NOW`를 누르면 **해당 상품 예약으로 가지 않고 홈으로 돌아간다.**

### 올바른 동작

상품 상세의 `BOOK NOW`는 반드시 다음 중 하나여야 한다.

1. 같은 페이지의 해당 상품 Bókun 예약 영역으로 smooth scroll
2. 해당 상품 예약 modal 열기
3. 해당 상품 `bokun_booking_url`로 직접 연결

**홈으로 보내면 안 된다.**

---

## 4.5 `/tours`에 노출되는 Seoul Tour 처리

영문 `/tours`에는 현재 다음 상품이 노출된다.

`Seoul Tour with Lunch and Hanbook`

문제:

- 뉴질랜드/오클랜드 전문 여행사 카탈로그 문맥과 맞지 않는다.
- 한글 사이트 canonical 9상품 목록에는 없다.
- `Hanbook`은 일반적으로 `Hanbok` 오타로 보인다.

### 작업

- 뉴질랜드 public catalog에서는 숨긴다.
- 실제 운영 목적이 있다면 별도 market/category에서만 노출한다.
- 삭제 전에 Bókun 운영 필요 여부는 확인한다.

---

# 5. P0/P1 — 데이터와 콘텐츠 오류 정리

## 5.1 Sunset 페이지

현재 확인된 문구:

`Wine Tasting & Hot Poll all included`

### 수정

`Hot Poll` → `Hot Pool`

---

## 5.2 접근성 정보 모순 제거

일부 상세 페이지에 아래가 동시에 존재한다.

- `Wheelchair accessible`
- `Not wheelchair accessible`

특히 Sunset, Half Day 계열에서 중복/모순이 확인된다.

### 작업

- Bókun product source에서 실제 조건 확인
- 정확한 항목 하나만 표시
- 데이터가 없으면 임의 판단하지 말고 문구를 숨긴다.

---

## 5.3 중복 포함/불포함 항목 정리

예:

- WiFi / WiFi on board 중복
- Fuel surcharge 중복
- Tip or gratuity / Gratuities 중복

### 작업

UI 렌더링 전에 중복 normalize 또는 product content 자체 수정.

---

## 5.4 이메일 주소 통일

새 홈/일부 최신 상세 페이지는:

`elimtournz@gmail.com`

일부 구형 상세 페이지는 아직:

`nzpraise@gmail.com`

### 목표

사이트 전체에서 공식 연락처를 `elimtournz@gmail.com`으로 통일한다.

다음 모두 확인:

- Header/Footer
- Product detail
- Contact
- structured data
- mailto
- Open Graph/metadata
- cached/legacy template

---

# 6. P0 — 한글 문의 폼의 가짜 성공 처리 수정

현재 한글 사이트 `app.js`는 `#inquiry-form` submit 시:

1. `preventDefault()`
2. 성공 alert 표시
3. form reset

만 수행한다.

**실제 서버/이메일 전송이 없다.**

이 상태에서 고객에게 “정상적으로 접수되었습니다”라고 보여주면 안 된다.

### 구현 요구사항

아래 중 repo/hosting 구조에 가장 맞는 실제 전송 방식을 구현한다.

- Vercel Serverless/Route Handler
- 기존 문의 API
- 검증된 email provider/API
- 기존 CRM/webhook

### 필수 UX

- sending 상태
- success 상태
- error 상태
- 중복 제출 방지
- required validation
- spam/honeypot 또는 최소한의 bot protection

### 중요

백엔드가 준비되지 않았다면 **거짓 성공 alert를 제거**하고 실제 연락처/WhatsApp/mailto로 안내한다.

---

# 7. P1 — 영문 사이트 Reviews 섹션 강화

영문 홈에는 `Reviews from Tripadvisor` 제목은 있으나 실제 리뷰 콘텐츠가 제대로 노출되지 않는다.

한글 사이트에는 이미 다음이 잘 구현되어 있다.

- TripAdvisor 5.0 신뢰 표시
- 실제 고객 후기 4개
- 고객 국가
- 투어 이름
- TripAdvisor 원문 링크

### 작업

한글 사이트의 review card 구조를 영문 홈에도 적용한다.

### 디자인

- 3~4 cards desktop
- horizontal carousel 또는 stacked cards mobile
- 과한 별 아이콘/초록색 장식 금지
- Tripadvisor 브랜드를 모방하는 가짜 UI 금지
- 실제 링크와 실제 review text만 사용

---

# 8. P1 — 홈 정보 구조 재설계

## 8.1 Hero

현재 메시지의 핵심은 좋다.

`New Zealand, at your pace.`

이를 브랜드 중심 메시지로 유지한다.

### Hero 권장 구조

**Eyebrow**  
`PRIVATE TOURS · AUCKLAND, NEW ZEALAND`

**H1**  
`New Zealand, at your pace.`

**Subcopy**  
짧고 한 줄 또는 두 줄.

**Primary CTA**  
`Explore Tours`

**Secondary CTA**  
`Plan a Private Tour`

### 시각적 요구

- 뉴질랜드 실제 풍경을 full-bleed로 크게 사용
- 텍스트 대비 충분히 확보
- gradient overlay는 최소한
- 관광청/패키지여행식 파란색 과다 사용 금지
- 프리미엄이지만 지나치게 럭셔리 호텔처럼 만들지 않는다.

---

## 8.2 Hero 바로 아래 Trust Bar

한글 사이트의 장점을 가져온다.

- `Tripadvisor 5.0`
- `NZ Licensed Passenger Operator`
- `Auckland Hotel Door-to-Door`

desktop 한 줄 / mobile 3행 또는 swipe.

---

## 8.3 중복되는 4가지 장점 섹션 압축

현재 홈에는 앞쪽 4 Pillars와 뒤쪽 `The Elim Promise`가 의미상 많이 겹친다.

### 목표

- 앞쪽: **검증 가능한 신뢰 요소**
- 뒤쪽: **Elim 브랜드 스토리와 여행 철학**

으로 역할을 구분한다.

중복 문장은 제거한다.

---

# 9. P1 — Tour Card 디자인 개선

현재 카드 설명이 길고 상품 간 구조가 불명확하다.

### 카드 기본 정보

1. 이미지
2. type badge
   - `PRIVATE`
   - `SMALL GROUP`
3. 상품명
4. duration
5. group size
6. 가격이 안정적으로 제공될 경우 `From NZ$...`
7. CTA
   - `View Tour`
   - 필요시 `Book Now`

### 중요

한글 사이트 카드 코드에서 현재 `Duration`을 카드 image badge와 footer에서 **두 번 표시**한다.

가격 데이터가 없다면 두 번째 duration을 반복하지 말고:

- `Check availability`
- `View dates`
- `Book this tour`

같은 action UI로 바꾼다.

가격을 임의 하드코딩하지 않는다.

---

# 10. P1 — Small Group vs Private를 명확히 구분

현재 아래 두 상품은 일반 고객에게 매우 비슷하게 보인다.

- `Auckland Half Day City Tour - Heart of Auckland`
- `[PRIVATE] Auckland Half Day City Tour`

### 디자인/카피로 즉시 구분

#### Small Group

- SMALL GROUP badge
- Per person pricing이라는 사실이 맞다면 명확히 표기
- shared vehicle/group 설명

#### Private

- PRIVATE CHARTER badge
- `Private group · up to 8 guests`
- group price이면 `per group` 표시

**가격 단위는 Bókun 실제 설정을 확인 후 표기한다. 추측 금지.**

---

# 11. P1 — 상품 상세 화면을 가장 크게 개선

영문 구형 상세 페이지는 이미지가 길게 이어지고, 예약 CTA가 약하며, header/footer가 홈과 다르다.

### 목표 레이아웃

#### Desktop

- 상단: 정돈된 4~5장 gallery
- main layout: 2 column
  - Left: title / summary / highlights / itinerary / includes / excludes / pickup / FAQ
  - Right: **sticky booking card**

#### Booking card

- Tour name
- duration
- group type
- 정확한 price/availability
- Bókun calendar/widget
- `Book This Tour`
- secure booking reassurance

#### Mobile

- gallery
- summary
- 핵심 정보
- 하단 sticky `Check dates / Book` CTA
- Bókun 위젯은 화면폭에 맞게 overflow 없이 동작

### 한글 사이트 참고

한글 사이트의 `openTourModal()` 2-column 구조와 Bókun iframe 매핑은 기능적으로 좋은 출발점이다.

영문 사이트에서도 이 interaction model을 재사용하거나 공통 컴포넌트로 만든다.

---

# 12. P1 — Header / Footer / Detail 템플릿 통일

현재 영문 상세 페이지는 서로 다른 세대의 template이 섞여 있다.

예:

- 일부 구형 상세: old logo / Home-Tours-Contact / old email
- 일부 최신 상세: 새 ELIM header/footer 스타일
- Home: 별도의 새 디자인

### 목표

다음 경로가 **한 사이트처럼 보여야 한다.**

- `/`
- `/tours`
- `/tour/*`
- Korean site equivalent

공통:

- Logo
- Navigation
- Typography
- Color tokens
- CTA style
- Footer
- spacing system

---

# 13. P1 — 한글 사이트의 정상 구조를 영문 사이트에 반영

한글 사이트에서 특히 유지/재사용할 것:

1. 9개 canonical tour data
2. category filters
3. tour detail modal
4. Bókun booking iframe per product
5. TripAdvisor review cards
6. trust bar
7. Korean translations
8. SEO metadata / hreflang 개념

단, 다음은 그대로 복사하지 말고 개선한다.

- contact form fake success
- placeholder legal links
- duplicated duration UI
- 너무 복잡한 logo DOM

---

# 14. P2 — 데이터 구조 통합

### 권장 모델

하나의 canonical tour object가 다음을 가진다.

```ts
interface Tour {
  slug: string;
  productId: string;
  bokunBookingUrl: string;
  category: 'city' | 'nature' | 'day' | 'golf' | 'transfer';
  tourType: 'private' | 'group';
  duration: string;
  groupSize?: string;
  image: string;
  gallery: string[];
  en: {
    title: string;
    tagline: string;
    summary: string;
    highlights: string[];
    includes: string[];
    excludes: string[];
  };
  ko: {
    title: string;
    tagline: string;
    summary: string;
    highlights: string[];
    includes: string[];
    excludes: string[];
  };
}
```

### 원칙

- English/Korean data duplication 최소화
- slug/productId는 language-independent
- 언어별 텍스트만 locale field
- Bókun 가격/availability를 가져올 수 있다면 실시간 데이터 사용
- 불가능하면 가격을 중복 하드코딩하지 않는다.

---

# 15. P2 — SEO / URL 정책

### 유지할 것

- `lang="ko"` / `lang="en"`
- canonical
- hreflang `ko`, `en`, `x-default`
- Open Graph
- TravelAgency structured data
- FAQ structured data

### 추가

- Tour/Product 구조화 데이터가 유효하다면 각 상품에 적용
- unique title/description
- descriptive alt text
- legacy URL 301 redirects
- sitemap에 canonical routes만 포함

### 한국어 사이트 도메인

이번 작업에서 **무리하게 도메인을 이전하지 않는다.**

향후 `nzelimtour.com/ko`로 통합할 여지는 있지만, 현재 배포 구조를 확인한 뒤 안전한 migration plan 없이 URL을 바꾸지 않는다.

---

# 16. 브랜드 디자인 방향

## 핵심 키워드

- Quiet premium
- New Zealand nature
- Rest / oasis
- Private journey
- Local care
- Calm confidence

## 피해야 할 것

- 흔한 비행기 아이콘
- 지도 핀 중심의 여행사 로고
- 야자수/지구본
- 과한 파랑+초록 그라데이션
- 패키지여행 전단지 느낌
- 지나친 gold luxury
- 불필요한 glassmorphism
- 과도한 animation

## 권장 팔레트

CSS token 형태로 정리한다.

- `--forest`: deep pounamu / forest green
- `--ink`: near-black green/charcoal
- `--sand`: warm ivory/sand
- `--water`: muted mineral aqua
- `--sun`: restrained warm sandstone accent

정확한 hex는 기존 사진/contrast를 보고 정하되 WCAG AA를 만족시킨다.

---

# 17. Typography

### 목표

- Heading: editorial하고 차분한 character
- Body/UI: 매우 읽기 쉬운 modern sans

### 원칙

- 2 font families 이상 과다 사용 금지
- 실제 라이선스가 안전한 웹폰트 또는 기존 프로젝트 font 사용
- body 16px 이하로 지나치게 작게 만들지 않는다.
- 한국어 글꼴은 영문 font의 대체가 부자연스러우면 시스템 Korean sans stack 사용

---

# 18. 로고 리디자인

현재 한글 소스에서는 header에 다음이 동시에 존재한다.

- text wordmark
- PNG logo
- 복잡한 inline SVG

footer에도 또 다른 logo SVG/image가 존재한다.

이 구조를 정리해 **하나의 canonical logo system**으로 통합한다.

## 18.1 새 로고 방향

아래 이미지를 **방향성 reference**로 사용한다.

![ELIM TOUR logo direction](./assets/logo-direction-reference.png)

### 의미

- 산 / 뉴질랜드 자연
- 물결 / 오아시스 / 쉼
- 해가 떠오르는 느낌
- 여행길의 부드러운 흐름
- `ELIM`이 가장 먼저 읽히는 구조

### 로고 구조

- 심볼 + `ELIM`
- 아래 또는 보조 위치에 작은 `TOUR`
- `ELIM` 가독성이 핵심

### 금지

- 비행기 아이콘
- 국기 직접 삽입
- fern을 너무 literal하게 사용
- 복잡한 디테일
- small size에서 사라지는 얇은 선

---

## 18.2 Codex deliverables — logo

가능하면 직접 SVG asset으로 정리한다.

필수:

1. `logo-horizontal.svg`
2. `logo-mark.svg`
3. `logo-white.svg`
4. `logo-onecolor.svg`
5. favicon/app icon용 simplified mark

추가:

- PNG 1x/2x export가 build pipeline에 필요할 경우만 생성

### 검수

- 24px 높이 header에서도 ELIM 식별 가능
- 16~32px favicon에서 mark 인식 가능
- white / dark background 양쪽 모두 사용 가능
- monochrome에서도 형태 유지

---

# 19. 한국어 사이트에서 추가로 수정할 것

투어 예약 링크는 정상이다. 아래는 **별도의 개선 항목**이다.

## 19.1 문의 폼 실제 전송

앞서 설명한 mock alert 제거.

## 19.2 Terms & Privacy

현재 footer:

- Terms & Conditions → `#contact`
- Privacy Policy → `#contact`

### 작업

실제 정책 페이지가 있으면 연결한다.

없으면:

- placeholder 링크를 노출하지 않거나
- 실제 간단한 policy page를 작성한다.

가짜 legal navigation을 남기지 않는다.

## 19.3 전화번호 표기 통일

표시는 읽기 쉽게:

`+64 21 931 004`

실제 `tel:` href는:

`tel:+6421931004`

형태로 유지.

---

# 20. Mobile UX

반드시 실제 모바일 viewport로 검수한다.

### Header

- logo가 너무 작아지지 않음
- hamburger touch target 44px 이상
- menu open 시 body scroll 문제 없음

### Tour cards

- 1 column
- CTA touch target 충분
- 제목 3~4줄로 무한 증가하지 않도록 관리

### Detail modal/page

- 좌우 2-column을 단순히 축소하지 말 것
- mobile에서는 자연스럽게 vertical stack
- booking CTA를 하단 sticky로 제공
- modal close button 항상 접근 가능

### Bókun iframe

- horizontal overflow 없음
- iOS Safari에서 내부 스크롤 사용성 확인

---

# 21. Accessibility

최소 기준:

- WCAG AA contrast
- keyboard navigation
- visible focus
- modal focus trap
- ESC close
- `aria-modal`, `aria-label`
- image alt
- heading hierarchy
- button vs link semantic 구분
- form label 연결
- error messages screen reader 노출
- prefers-reduced-motion 고려

---

# 22. Performance

### 목표

- Hero image responsive source / compression
- lazy loading below fold
- width/height 또는 aspect-ratio 지정하여 CLS 최소화
- 불필요한 slideshow/autoplay 최소화
- 과도한 image gallery 초기 로드 금지
- inline SVG 중복 제거
- duplicated CSS/JS 정리

### 확인

Lighthouse mobile 기준:

- Performance: 가능한 한 85+
- Accessibility: 95+
- Best Practices: 95+
- SEO: 95+

점수 자체보다 실제 문제 해결을 우선한다.

---

# 23. 상품 상세 콘텐츠 규칙

Bókun의 원천 데이터가 이상한 경우 프론트에서 조용히 감추는 것만으로 끝내지 말고 가능하면 원천도 정리한다.

### 표시 순서

1. One-line value proposition
2. Duration / type / group size
3. Highlights
4. Itinerary
5. Included
6. Not included
7. Pickup
8. Important notes
9. FAQ / cancellation
10. Reviews
11. Booking

### 문체

- 짧고 명확
- 과한 “luxury / unforgettable / breathtaking” 반복 금지
- 뉴질랜드 현지 전문성과 편안함 중심

---

# 24. Acceptance Criteria — 반드시 모두 통과

## 링크

- [ ] 영문 홈 9개 tour card 모두 정상
- [ ] Golf old slug는 301 후 canonical page
- [ ] Sunset old slug는 301 후 canonical page
- [ ] 모든 detail `BOOK NOW`가 자신의 Bókun 상품으로 연결
- [ ] 한글 9개 Bókun product mapping 변경 없음
- [ ] `/tours`와 홈 tour inventory 일치
- [ ] Seoul 상품은 의도하지 않는 한 NZ public catalog에 없음
- [ ] Tripadvisor external link 정상
- [ ] Terms/Privacy가 placeholder가 아님

## 데이터

- [ ] `Hot Poll` 오타 없음
- [ ] Wheelchair accessible 모순 없음
- [ ] 중복 WiFi/Gratuity/Fuel 항목 없음
- [ ] 공식 이메일이 `elimtournz@gmail.com`으로 통일
- [ ] duration/group/pricing 단위가 각 상품에서 일관됨

## 디자인

- [ ] Home / Tours / Detail / Korean이 같은 브랜드 시스템
- [ ] ELIM logo가 header에서 선명
- [ ] tour type이 즉시 구분
- [ ] Trust bar 존재
- [ ] English reviews 실제 노출
- [ ] detail booking CTA가 항상 쉽게 접근 가능

## 문의

- [ ] Korean contact form 실제 전송 또는 정직한 fallback
- [ ] 가짜 success alert 없음
- [ ] loading/error/success state 존재

## Responsive

- [ ] 1440 desktop 검수
- [ ] 1024 tablet 검수
- [ ] 390 mobile 검수
- [ ] 가로 overflow 없음
- [ ] modal/booking iframe 모바일 정상

## 접근성/품질

- [ ] keyboard navigation
- [ ] modal focus trap
- [ ] visible focus
- [ ] alt text
- [ ] heading hierarchy
- [ ] console error 없음
- [ ] broken internal link 없음

---

# 25. 작업 후 Codex가 제출할 결과물

1. 변경된 코드
2. `CHANGELOG.md` 또는 작업 요약
3. before/after screenshots
   - Desktop Home
   - Mobile Home
   - Tours
   - Private Tour Detail
   - Korean Home
   - Korean Tour Modal
4. link-check 결과
5. 9개 Bókun booking mapping 검증 결과
6. Lighthouse 간단 요약
7. 남아 있는 운영 데이터 문제 목록

---

# 26. 권장 작업 순서

### Phase 1 — Safety / Booking

1. repo 구조 파악
2. canonical tour data 확정
3. Golf/Sunset broken slug 수정 + redirect
4. detail `BOOK NOW` 수정
5. 한글 Bókun mapping regression test
6. Seoul catalog 처리

### Phase 2 — Data cleanup

7. 구형 footer/email 제거
8. product content typo/duplicate/accessibility 정리
9. 문의 폼 실제 전송 구현
10. legal links 정리

### Phase 3 — Design system

11. color / typography tokens
12. 새 logo assets
13. header/footer 통합
14. home hero + trust bar
15. tour cards
16. reviews

### Phase 4 — Detail UX

17. gallery
18. 2-column detail
19. sticky Bókun booking card
20. mobile sticky CTA

### Phase 5 — QA

21. desktop/mobile screenshots
22. all-link crawl
23. all 9 booking test
24. accessibility check
25. Lighthouse
26. final regression

---

# 27. 최종 브랜드 방향 한 문장

> **ELIM TOUR should feel like a calm, trustworthy local New Zealand private-tour specialist — not a generic booking marketplace and not a flashy luxury travel agency.**

사용자가 사이트에서 받아야 하는 첫 인상은 다음이다.

> **“현지에서 믿고 맡길 수 있고, 내 일행의 속도에 맞춰 편안하게 뉴질랜드를 보여주는 여행사.”**

---

# 28. Codex에게 마지막 지시

- 기능적으로 정상인 한글 사이트의 **Bókun 링크를 리팩터링 과정에서 깨뜨리지 말 것.**
- 디자인만 예쁘게 만들고 예약 기능을 후퇴시키지 말 것.
- existing product ID를 추측으로 변경하지 말 것.
- 실제 브라우저로 모든 메뉴/카드/모달/CTA를 직접 클릭해 확인할 것.
- 수정 후 스크린샷과 링크 검증 결과를 남길 것.
- production deploy 전에 preview에서 최종 검수할 것.

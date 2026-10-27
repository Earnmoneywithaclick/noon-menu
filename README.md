# NOON Menu

카페 NOON 테이블 QR 메뉴판 — https://cafenoonmenu.com/

GitHub Pages(저장소 Earnmoneywithaclick/noon-menu)로 배포. main 에 push 하면 1분 안에 반영된다.
도메인은 Cloudflare 에서 구입, DNS 는 GitHub Pages A 레코드 4개 + www CNAME (모두 DNS only).

```
index.html        메뉴 페이지 (QR 이 가리키는 곳)
css/style.css     스타일
js/menu-data.js   메뉴·가격·가게 정보  ← 메뉴 수정은 여기만
js/app.js         히어로 연출 + 메뉴 렌더링
qr.html           테이블용 QR 카드 인쇄 페이지
NOON Menu.html    원본 아티팩트 (한 파일짜리 백업)
```

## 메뉴 수정

`js/menu-data.js` 의 `items` 만 고치면 된다. 저장 후 다시 배포하면 QR 은 그대로 둔 채 내용만 바뀐다.
가게 주소·영업시간·인스타그램은 같은 파일 위쪽 `cafe` 에 채우면 푸터에 표시된다.

## 1차 목표 — QR 배포

1. 호스팅에 폴더째 올린다 (무료: Cloudflare Pages / Netlify / GitHub Pages).
2. 배포된 주소의 `qr.html` 을 열면 주소가 자동으로 채워진 QR 카드가 나온다 → 인쇄.
3. **QR 에 들어가는 주소는 한 번 인쇄하면 못 바꾼다.** 호스팅 업체 주소(`xxx.pages.dev`)보다
   카페 소유 도메인(예: `menu.cafenoon.com`)을 연결해두면 나중에 호스팅을 옮겨도 QR 을 다시 찍을 필요가 없다.

로컬 확인: `python -m http.server 8080` 후 http://localhost:8080

## 2차 목표 — 미리 담고 POS 에서 한 번에 결제

흐름:

```
손님 폰 (메뉴 + 장바구니)  →  우리 서버(작은 API)  →  POS 업체 API 에 "주문" 생성
                                                       ↓
                               카운터 POS 에 주문이 뜸 → 손님은 이름/번호만 말하고 바로 결제
```

- **POS API 키는 절대 브라우저(js)에 넣으면 안 된다.** 그래서 정적 사이트 옆에 서버리스 함수
  하나(Cloudflare Workers / Netlify Functions 등)가 필요하다.
- 메뉴 항목마다 POS 상품 ID 를 매핑해야 한다 (`menu-data.js` 에 `pos` 필드 추가 예정).
  가능하면 메뉴 자체를 POS 카탈로그 API 에서 받아오게 해서 가격을 두 군데서 관리하지 않게 한다.
- `qr.html` 의 테이블별 QR(`?t=3`)로 어느 테이블 주문인지 함께 넘길 수 있다.
- 어떤 POS 를 쓰는지에 따라 구현이 완전히 달라진다 (Square·Clover·Lightspeed·Toast 등은 주문 생성 API 가 있음).

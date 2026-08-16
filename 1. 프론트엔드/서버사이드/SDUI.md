# SDUI(Server-Driven UI)

배포 없이 화면을 바꾸는 기법



## 사용 이유

 스토어 심사 속도의 한계를 극복



## 스키마 정의

1. 섹션 (Section): '오늘의 특가', '실시간 랭킹'처럼 독립적인 목적을 가진 컴포넌트 이상의 단위
2. 컴포넌트 (Component): 섹션을 구성하는 '상품 카드', '헤더 타이틀' 같은 최소 단위
3. 데이터 (Data): 컴포넌트에 채워질 실제 내용 (예: 상품명, 가격, 이미지 URL)
4. 액션 (Action): 버튼 클릭 시 특정 페이지로 이동하거나, 분석 로그를 보내는 등 사용자의 행동을 정의

### 1. 섹션(Section)

정확한 목적을 가지고 있는 독립적인 UI 단위

```jsonc
{
  "type": "TIME_DEAL_SECTION",
  "id": "10",
  "header": {
    "title": "타이틀",
    "style": "HEADER_LARGE"
  },
  "limitTime": "2025-12-31T23:59:59Z",
  "totalCount": 2,
  "itemList": [
    {
      "type": "CARD_TYPE",
      "id": 1,
      "code": "상품code",
      "title": "올리브영 상품1"
    },
    {
      "type": "CARD_TYPE",
      "id": 2,
      "code": "상품code",
      "title": "올리브영 상품2"
    }
  ]
}
```



### 2. 컴포넌트

하나의 섹션을 만들기 위해 여러 개의 컴포넌트가 조합될 수 있는 요소로 정의

```json
{
  "type": "HEADER_COMPONENT",
  "id": 10,
  "title": "타이틀",
  "style": "HEADER_LARGE" // HEADER_MEDIUM, HEADER_REGULAR
}
```

```json
{
  "type": "PRODUCT_CARD",
  "id": 12,
  "code": "10001",
  "title": "올리브영 상품",
  "price": 9900,
  "imageUrl": "https://...",
  "badge": "HOT"
}
```



### 3. 데이터

컴포넌트에 실제 내용을 채우는 '데이터'

Android, iOS, Web이 모두 같은 스키마를 바라보기 때문에, 이 '공통 언어'의 규칙을 명확히 정의하는 것이 무엇보다 중요

- 필수값 (Required): id, type처럼 이 값이 없으면 렌더링 자체가 불가능한 값
- 옵션값 (Optional): subtitle처럼 값이 없어도 UI가 깨지지 않는 값
- 기본값 (Default): isShow = false처럼, 값이 누락되었을 때 클라이언트가 안전하게 처리할 기본 상태
- 포맷 (Format): date 포맷, ImageUrl 경로 등 데이터의 형식을 통일

```json
{
  "type": "PRODUCT_CARD",
  "id": "12",
  "title": "상품명",
  "price": 9900,
  "imageUrl": "이미지 URL",
  "badge": "SALE",
  "rating": 4.6,
  "reviewCount": 0,
  "isShow": false
}
```



### 4. 액션

각 컴포넌트의 이벤트 및 로그 등을 담당.

Action의 경우 Type에 따라 동작이 달라짐.

- Link : URL을 통해 외부,내부 브라우저로 이동 하거나 딥링크가 동작
- API : API BaseUrl을 제외한 Path 정보를 제공함으로써 앱에서 별도의 하드코딩 없이 API를 요청

```jsonc
{
  "type": "PRODUCT_CARD",
  // .....
  "handler": {
    "action": {
      "type": "Link",
      "value": "url 정보"
    },

    //1안
    "event1": {
      "amplitude": {
        "name": "product_select",
        "parameters": { "param1": "value1", "param2": "value2" }
      },
      "appsflyer": {
        "name": "product_select",
        "parameters": { "param1": "value1", "param2": "value2" }
      }
    },

    //2안
    "event2": [
      {
        "type": "amplitude",
        "event_type": "impression",
        "name": "product_impression",
        "parameters": { "param1": "value1", "param2": "value2" }
      },
      {
        "type": "appsflyer",
        "event_type": "click",
        "name": "product_select",
        "parameters": { "param1": "value1", "param2": "value2" }
      }
    ]
  }
}
```

Event의 경우 1안은 명시적이고, 이벤트 타입이 한 곳에 모여 있는 장점이 있지만, 새로운 이벤트 추가 시 확장성이 떨어지는 단점이 있음.

2안이 적합한 이유에는 '확장성' 외에 '플랫폼 간 일관성'이나 '로깅 정책 통일' 관점에서도 유효한 점이 있어 권장하지만, 각 이벤트별로 파라미터 및 제공하는 스펙이 다르면 데이터 구조를 통일하는 어려움이 있음. 

## 참고자료

[[https://oliveyoung.tech/2025-11-04/server-driven-technical-exploration/]]([https://oliveyoung.tech/2025-11-04/server-driven-technical-exploration/]](https://oliveyoung.tech/2025-11-04/server-driven-technical-exploration/]))([올리브영 테크블로그] SDUI로 네이티브 운영 민첩성 높이기)
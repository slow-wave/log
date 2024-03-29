---
title: "[project] 토이 프로젝트 리팩토링 1 - REST API"
date: 2023-02-20T18:03:37+09:00
draft: false
tags: ["Backend", "REST API"]
categories: ["project"]
showToc: true
UseHugoToc: true
comments: true
---

최근에 영화 리뷰를 저장할 수 있는 웹 서비스를 개발하는 토이 프로젝트를 진행했습니다. MERN(MongoDB, Express, React, Node) 스택으로 개발했으며 TMDB API의 영화 정보를 활용했습니다. 왓챠피디아 같은 커뮤니티형보다는 개인 기록형 영화 리뷰 저장소를 만드는 것을 목표로 했습니다. 그래서 주 기능은 1) 영화를 탐색하고 2) 북마크하고 3) 리뷰를 작성하는 것입니다.

정의한 요구사항 대로 구현하니 기능은 잘 동작하지만 코드 상에는 많은 문제와 개선해야 할 점이 있다는 것을 알게되었습니다. 점차적으로 개선하면서 공부한 것들을 기록하려고 합니다. 제가 생각하는 가장 큰 문제는 잘못 설계된 HTTP API입니다. 요청을 보내는 주소만으로는 어떤 것을 요청 하는지 파악이 불가능하고 적절한 HTTP 상태 코드를 사용하지 않았습니다. 그래서 RESTful한 API로 수정하는 작업을 진행했습니다. 우선 REST API란 무엇인지 알아보겠습니다.

## REST API란?

**REST**는 네트워크 아키텍처 원리의 모음입니다. '네트워크 아키텍처 원리'란 자원을 정의하고 자원에 대한 주소를 지정하는 방법을 의미합니다. 다시말하면 자원(resource)의 표현(representation)에 의한 상태 전달을 뜻합니다.

## REST API Quick Tips

REST 스타일을 위해서는 다음의 내용을 고려해야합니다.

### 첫번째, HTTP method를 지정해 무엇을 요청하는지 표현한다.

HTTP method는 클라이언트가 웹서버에게 요청하는 목적 및 종류를 알리는 수단입니다. HTTP method의 종류에는 GET, POST, DELETE, PUT, PATCH, HEAD, OPTIONS 등이 있습니다.

- GET - 서버에게 Resource를 보내도록 요청
  - URL(URI) 형식으로 웹서버측 리소스(데이터)를 요청
- POST - 클라이언트에서 서버로 Resource 제출
  - 요청 데이터를 HTTP body에 담아서 전달
- DELETE - 웹 리소스 제거
- PUT - 웹 리소스 수정
- PATCH - 웹 리소스의 일부분 수정
- HEAD - 메세지 헤더 얻기
  - GET과 비슷하지만 실제 문서를 요청하는 것이 아니라 문서에 대한 정보를 요청

### 두번째, 적절한 리소스(URL 경로) 이름을 만든다.

URL(URI) 이름을 보고 어떤 자원을 요청하는 것인지 알 수 있어야합니다. 리소스명을 정할 때 고민이 되어 IT회사에서 공개하는 REST API 명세서를 찾아본 적이 있습니다. MS의 경우 [Microsoft's internal company-wide REST API design guidelines](https://github.com/microsoft/api-guidelines)을 제공합니다. 가이드라인을 살펴보면 URL 네이밍에 대한 규칙도 존재합니다.

일반적인 리소스 이름에 대한 규칙은 다음과 같습니다.

- query-string 대신에 identifiers를 사용함.
  - Recommended: `/users/12345`
  - Not: `/api?type=user&id=12345`
- 리소스 이름은 명사임.
  - Recommended: `/users/12345/getcomments`
  - Not:`/users/12345/comments`
- URL segments에는 복수형을 사용함.
  - Recommended: `/customers/33245/orders/8769`
  - Not: `/customer/33245/order/8769`
- URL segments에는 소문자를 사용함. 단어의 분리가 필요하면 underscores(‘\_’) 나 hyphens(‘-’)를 사용함.
- URL은 가능한 짧게 하고 최대한 적은 세그먼트를 사용함.

### 세번째, HTTP Response Codes를 사용해 상태를 나타낸다.

HTTP 상태 코드는 클라이언트가 보냈던 요청의 수행 결과를 의미하는 일종의 약속이며, API를 구성하는 중요한 요소 중 하나입니다. 클라이언트가 서버에게 작업을 요청하면 서버는 요청받은 작업을 수행한 후 작업의 수행 결과를 응답을 보냅니다. 이때 HTTP 상태 코드를 사용하여 작업의 성공/실패 여부와 작업이 실패했다면 어떤 이유로 실패했는지도 알려줍니다. HTTP 상태 코드에서 100번대는 정보 확인, 200번대는 통신 성공, 300번대는 리다이렉트, 400번대는 클라이언트 오류, 500번대는 서버 오류를 의미합니다.

자주 사용하는 HTTP response code는 다음과 같습니다.

- HTTP response code
  |상태코드| 이름 | 설명 |
  |-|---|---|
  |200|OK|가장 흔하게 쓰이는 일반적인 성공 상태 코드임.|
  |201|CREATED| 요청 성공 & 리소스가 새로 생성되었음을 의미함.(예 - POST, PUT) case - 회원가입을 통해 새로운 유저 정보가 생성됨.|
  |204|NOT CONTENT| 요청 성공 & 요청과 관련되었던 컨텐츠가 없음을 의미함. (예 - DELETE, PUT case - 게시글 삭제|
  |400|BAD REQUEST| 클라이언트가 서버에게 보낸 요청이 잘못된 경우를 의미함.|
  |401|UNAUTHORIZED| 인증되지 않은 사용자가 인증이 필요한 리소스를 요청하는 경우|
  |403|FORBIDDEN| 사용자가 작업을 수행할 권한이 없거나 리소스를 사용할 수 없는 경우임. case - HTTPS 프로토콜로만 접근해야하는 리소스에 HTTP 프로토콜을 사용하여 접근했을 경우|
  |404|NOT FOUND| 요청한 리소스가 존재하지 않을 경우임.|
  |405|METHOD NOT ALLOWED| 요청한 URL은 존재하지만 요청한 HTTP method가 적용되지 않음을 나타낼 때 사용함. |

위의 내용을 참고해서 기존의 API를 변경하는 작업을 진행했습니다. 다음은 리뷰와 관련한 API입니다.

- 수정 전
  - 리뷰 상세 조회 `/api/review/getOneReview`
  - 리뷰 등록 `/api/review/submit`
  - 리뷰 수정 `/api/review/edit`
  - 리뷰 삭제 `/api/review/removeFromReview`
- 수정 후
  - 리뷰 상세 조회 `GET /api/reviews/{reviewId}`
  - 리뷰 등록 `POST /api/reviews`
  - 리뷰 수정 `PATCH /api/reviews/{reviewId}`
  - 리뷰 삭제 `DELETE /api/reviews/{reviewId}`

## GraphQL

GraphQL은 페이스북에서 만든 쿼리언어로 REST API를 대체할 수 있는 방법입니다. CSR(Client Side Rendering) 환경에서는 정교한 HTTP API 사용을 위해서 GraphQL을 사용합니다. gql은 웹 클라이언트가 데이터를 서버로 부터 효율적으로 가져오는 것이 목적입니다. 간단하게 REST API와 GraphQL를 비교하겠습니다. 둘 다 리소스를 URL로 식별하고 이를 통해 앱이 데이터나 기능을 가져올 수 있다는 공통점이 있습니다. 하지만 REST API의 경우 URL, METHOD등을 조합하기 때문에 다양한 Endpoint가 있는 반면 GraphQL은 단 하나의 endpoint가 있다는 차이점이 있습니다.

REST API와 GraphQL은 SSR인지 CSR 환경인지에 따라서 결정되는 듯 보입니다. 다음에는 GraphQL과 REST API를 자세히 비교해보고 활용 사례를 알아보겠습니다.

## 참고자료

- [[wiki] REST](https://ko.wikipedia.org/wiki/REST)
- [[blog] REST란? REST API 와 RESTful API의 차이점](https://dev-coco.tistory.com/97)
- [[docs] REST API Quick Tips](https://www.restapitutorial.com/lessons/restquicktips.html)
- [[blog] 서버의 상태를 알려주는 HTTP 상태 코드](https://evan-moon.github.io/2020/03/15/about-http-status-code/)
- [[blog] GraphQL: API 소비자에 대한 일관된 접근방식 수립](https://cloud.google.com/blog/ko/products/api-management/interacting-with-apis-rest-and-graphql)
- [[blog] GraphQL 개념잡기](https://tech.kakao.com/2019/08/01/graphql-basic/)

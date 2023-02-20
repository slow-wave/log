---
title: "[MOVIE APP] 토이 프로젝트 API 리팩토링"
date: 2023-02-20T18:03:37+09:00
draft: false
tags: ["Backend", "REST"]
categories: ["project"]
showToc: true
UseHugoToc: true
comments: true
---

최근에 영화 리뷰를 저장할 수 있는 웹 서비스를 개발하는 토이 프로젝트를 진행했습니다. MERN(MongoDB, Express, React, Node) 스택으로 개발했으며 TMDB API의 영화 정보를 활용했습니다. 왓챠피디아 같은 커뮤니티형보다는 개인 기록형 영화 리뷰 저장소를 만드는 것을 목표로 했습니다. 그래서 주 기능은 1) 영화를 탐색하고 2) 북마크하고 3) 리뷰를 작성하는 것입니다.

정의한 요구사항 대로 구현하니 기능은 잘 동작하지만 코드 상에는 많은 문제가 있다는 것을 알게되었습니다. 점차적으로 개선하면서 공부한 것들을 기록하려고 합니다. 제가 생각하는 가장 큰 문제는 잘못 설계된 HTTP API입니다. 요청을 보내는 주소만으로는 어떤 것을 요청 하는지 파악이 불가능하고 적절한 HTTP 상태 코드를 사용하지 않았습니다. 그래서 RESTful한 API로 수정하는 작업을 진행했습니다. 우선 REST API란 무엇인지 알아보겠습니다.

## REST API란?

**REST**는 네트워크 아키텍처 원리의 모음입니다. '네트워크 아키텍처 원리'란 자원을 정의하고 자원에 대한 주소를 지정하는 방법을 의미합니다. 다시말하면 **자원(resource)**의 **표현(representation)**에 의한 **상태 전달**을 뜻합니다.

REST API를 만들기 위해서는 다음의 내용을 고려해야합니다.

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

HTTP 상태 코드는 클라이언트가 보냈던 요청의 수행 결과를 의미하는 일종의 약속이며, API를 구성하는 중요한 요소 중 하나입니다. 클라이언트가 서버에게 작업을 요청하면 서버는 요청받은 작업을 수행한 후 작업의 수행 결과를 응답을 보내줍니다. 이때 HTTP 상태 코드를 사용하여 작업의 성공/실패 여부와 작업이 실패했다면 어떤 이유로 실패했는지도 알려줍니다. HTTP 상태 코드에서 100번대는 정보 확인, 200번대는 통신 성공, 300번대는 리다이렉트, 400번대는 클라이언트 오류, 500번대는 서버 오류를 의미합니다.

자주 사용하는 HTTP response code는 다음과 같습니다.

- HTTP response code

위의 내용을 참고해서 기존의 API 설계를 변경하는 작업을 진행했습니다. 예를 들어 리뷰와 관련한 CRUD 작업이 필요해 다음과 같이 API URI를 정의했습니다. 리소스 이름에 동작을 나타내며 고유한 식별자가 아니라는 문제점이 있습니다.

- 리뷰 상세 조회 `/api/review/getOneReview`
- 리뷰 등록 `/api/review/submit`
- 리뷰 수정 `/api/review/edit`
- 리뷰 삭제 `/api/review/removeFromReview`

그래서 다음과 같이 변경했습니다.

- 리뷰 상세 조회 `GET /api/reviews/{reviewId}`
- 리뷰 등록 `POST /api/reviews`
- 리뷰 수정 `PATCH /api/reviews/{reviewId}`
- 리뷰 삭제 `DELETE /api/reviews/{reviewId}`

## 참고자료

- [[wiki] REST](https://ko.wikipedia.org/wiki/REST)
- [[blog] **REST란? REST API 와 RESTful API의 차이점**](https://dev-coco.tistory.com/97)
- [[docs] \***\*REST API Quick Tips\*\***](https://www.restapitutorial.com/lessons/restquicktips.html)
- [[blog] **서버의 상태를 알려주는 HTTP 상태 코드**](https://evan-moon.github.io/2020/03/15/about-http-status-code/)
- [docs] HTTP Request Method, HTTP Request Type HTTP 요청 메소드, HTTP 요청 종류 (http://www.ktword.co.kr/test/view/view.php?no=3791)

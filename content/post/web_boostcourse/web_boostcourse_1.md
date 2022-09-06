---
title: "[boostcourse 웹프로그래밍 풀스택] 1. Web개발의 이해 "
date: 2022-09-06T14:41:22+09:00
draft: false
tags: ["web","http","boostcourse 웹프로그래밍 풀스택"]
categories: ["fullstack_boostcourse"]
showToc: true
UseHugoToc: true
comments: true
---

'[boostcourse 웹프로그래밍 풀스택]은' 네이버 부스트코스의 “[웹프로그래밍(풀스택)](https://www.boostcourse.org/web316)” 강의를 듣고 공부한 내용을 정리한 시리즈입니다. 일단 저는 Spring 프레임워크를 배워보고 싶어서 이 강의를 수강하게 되었습니다. 전공 공부를 할 때 프로그래밍 언어나 프레임워크 등을 익히는데 가장 효과적이고 재밌었던 방법은 직접 구현 해보는 방법이었습니다. 저는 Node.js 프레임워크와 Java, Javascript 언어를 공부한 경험이 있어서 (기억은 가물가물하지만;) 해당 강의 수강에 도전하게 되었습니다. 

깊게 설명해주시는 것은 아니지만 관련해서 공부하면 좋을 주제들을 많이 던져주는 강의라고 생각합니다. 그래서 원래 강의안의 전체적인 구조를 그대로 가져오되, 개인적으로 깊게 파고 들어본 내용을 더해서 정리하려고 합니다. 

1. Web개발의 이해 
- "[1-1. HTTP 프로토콜의 이해](https://slow-wave.github.io/post/fullstack_boostcourse/web_boostcourse_1/)"  
- 1-2. browser의 동작  
- 1-3. 웹서버  
- 1-4. WAS  

## 1-1. HTTP 프로토콜의 이해

### HTTP (Hypertext Transfer Protocol) 정의

HTTP는 서버와 클라이언트가 인터넷 상에서 데이터를 주고받기 위한 프로토콜입니다.  프로토콜은 상호 간에 정의한 규칙을 의미하며 특정 기기 간에 데이터를 주고받기 위해 정의되었습니다. 오늘날에는 거의 모든 파일 형식을 HTTP 형식을 이용해 전송 가능합니다. 

### HTTP 특징

HTTP 통신은 connectionless를 기본 동작으로 가집니다. connection을 유지하게되면 지속적으로 resource가 사용되기 때문입니다. 따라서 connection 유지는 최소화되는 것이 좋습니다. 

### HTTP 작동 방식

HTTP 프로토콜로 데이터를 주고 받기 위해서는 Request를 보내고 Response를 받아야합니다. Client는 요청을 보내는 쪽을 의미하며 웹 관점에서는 브라우저를 의미합니다. Server는 요청을 받는 쪽을 의미하며 일반적으로 데이터를 보내주는 원격지의 컴퓨터를 의미합니다. 

### Server-Client 모델

{{< figure src="/images/web_boostcourse_1/1.png">}}

Server-Client는 1:N 구조로 연결되어 있으며 클라이언트의 요청이 있을 때 서버가 응답하는 단방향 통신입니다. 즉, 한 대의 서버에 다수의 클라이언트가 접속하여 서비스를 이용합니다. 다수의 사용자들이 공동으로 열람·복사·수정·관리하는 데이터를 여러 곳에 분산시키지 않고 단일한 중앙 서버에 저장하여 관리함으로써 데이터의 유일성과 통일성을 보장할 수 있습니다. 

HTTP 프로토콜로 클라이언트가 서버에 요청을 보내고 성공적으로 데이터를 받게되면 동시에 서버와의 연결이 해체되고 보내진 데이터의 결과도 동시에 잃어버리게 됩니다. 이를 Stateless protocol 이라고 부르며 문제를 해결하기 위해 cookie, session, token 기술이 등장했습니다. 

- Client란?

네트워크를 이용하여 서버 시스템에 연결된 PC나 스마트폰 등 사용자 측을 의미합니다. Client는 서버에서 받아온 데이터를 단말기 화면에 표시하고 사용자의 요청을 서버에 전달하기 위해 웹브라우저를 사용합니다. 

- server란?

통신망 상에서 다른 컴퓨터에 대해서 회선, 디스크 장치에 대한 접속을 제어하는 소프트웨어 혹은 컴퓨터입니다. web server, WAS, DB server 등이 있습니다. 

### 참고 자료

[1] [프런트엔드 개발자가 알아야하는 HTTP 프로토콜 Part 1](https://joshua1988.github.io/web-development/http-part1/)
[2] [서버-클라이언트](http://wiki.hash.kr/index.php/%EC%84%9C%EB%B2%84-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8)
[3] [Http Stateless / cookie / session / token](https://velog.io/@jrk9204/Stateless)
[4] [Client-Server Model](https://www.geeksforgeeks.org/client-server-model/)
[5] [HTTP(Hypertext Transfer Protocol)의 특징](https://kotlinworld.com/97)

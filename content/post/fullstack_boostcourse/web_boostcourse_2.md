---
title: "[boostcourse 웹프로그래밍 풀스택] 1-2. browser의 동작"
date: 2022-10-14T16:34:33+09:00
draft: false
tags: ["web","HTTP","boostcourse 웹프로그래밍 풀스택"]
categories: ["fullstack_boostcourse"]
showToc: true
UseHugoToc: true
comments: true
---

1. Web개발의 이해   
- [1-1. HTTP 프로토콜의 이해](https://slow-wave.github.io/post/fullstack_boostcourse/web_boostcourse_1/)  
- [1-2. browser의 동작](https://slow-wave.github.io/post/fullstack_boostcourse/web_boostcourse_2/)  
- 1-3. 웹서버  
- 1-4. WAS  


이 글은 "[How Browsers Work: Behind the scenes of modern web browsers](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)"의 번역본을 정리한 것입니다. 

## 1. broswer

### 1) broswer의 주요 기능
브라우저의 주요 기능은 사용자가 선택한 자원을 서버에 요청하고 브라우저에 표시하는 것입니다. 자원은 보통 HTML 문서이지만 다른 형태일 수 있으며, 자원의 주소는 URI(Uniform Resource Identifier)에 의해 정해집니다. 

### 2) browser의 기본 구조
{{< figure src="/images/fullstack_boostcourse/web_boostcourse_2/0.png">}}

- UI : 주소 표시줄, 이전/다음 버튼 등 요청한 페이지를 보여주는 창을 제외한 나머지 모든 부분입니다.
- Browser Engine : UI와 Rendering Engine 사이의 동작을 제어합니다.
- Rendering Engine : 요청한 컨텐츠를 표시합니다. HTML을 요청하면 HTML과 CSS를 파싱하여 화면에 표시합니다.
    - Chrome의 경우 대부분의 브라우저와 달리 각 탭마다 별도의 렌더링 엔진 인스턴스를 유지합니다. 각 탭은 독립된 프로세스로 처리됩니다.
- Networking : HTTP 요청과 같은 네트워크 호출에 사용됩니다. 이는 플랫폼 독립적인 UI이고 각 플랫폼 하부에서 실행됩니다.
- UI Backend : 콤보 박스와 창 같은 기본적인 장치를 그립니다. 플랫폼에서 명시하지 않은 일반적은 인터페이스로서,  OS UI 체계를 사용합니다.
- Javascript Interpreter : javascript 코드를 해석하고 실행합니다.
- Data Persistence : 자료를 저장하는 계층입니다. 쿠키를 저장하는 것과 같이 모든 종류의 자원을 하드디스크에 저장해야합니다. HTML5 명세에는 브라우저가 지원하는 ‘웹 데이터 베이스'가 정의되어있습니다.

## 2. Rendering Engine
렌더링 엔진은 요청 받은 내용을 브라우저 화면에 표시하는 역할을 합니다. 

- firefox는 Gecko engine
- chrome, safari는 webkit engine을 사용함.

### 1) Rendering Engine 동작 과정
렌더링 엔진은 통신으로부터 요청한 문서의 내용을 얻는 것으로 시작하는데 문서의 내용은 보통 8KB 단위로 전송됩니다. 

렌더링 엔진은 좀 더 나은 사용자 경험을 위해 가능하면 빠르게 내용을 표시하는데 모든 HTML을 파싱할 때까지 기다리지 않고 배치와 그리기 과정을 시작합니다. 네트워크로부터 나머지 내용이 전송되기를 기다리는 동시에 받은 내용의 일부를 먼저 화면에 표시하는 것입니다. 

{{< figure src="/images/fullstack_boostcourse/web_boostcourse_2/1.png">}}

[1] HTML 파싱
- 렌더링 엔진은 HTML 문서를 파싱하고 콘텐츠 트리 내부에서 태그를 DOM 노드로 변환

[2] 렌더 트리 구축
- 그 다음 외부 CSS파일과 함께 포함된 스타일 요소도 파싱한다. 스타일 정보와 HTML 표시 규칙은 렌더 트리라고 부르는 다른 트리를 생성

[3] 렌더 트리 배치 
- 배치란 각 노드가 화면의 정확한 위치에 표시되는 것을 의미한

[4] 렌더 트리 그리기
- UI 백엔드에서 렌더 트리의 각 노드를 가로지르며 형상을 만들어 내는 그리기 과정

## 3. **Parsing - general**
문서 파싱은 브라우저가 코드를 이해하고 사용할 수 있는 구조로 변환하는 것을 의미합니다. 파싱 결과는 보통 문서 구조를 나타내는 노드 트리인데 파싱 트리(parse tree) 또는 문법 트리(syntax tree)라고 합니다. 

### 1) 문법
파싱은 문서에 작성된 언어 또는 형식의 규칙에 따르는데 파싱할 수 있는 모든 형식은 정해진 용어와 구문 규칙에 따라야합니다. 이것을 [context free grammar](https://web.dev/howbrowserswork/#context_free_grammar)라고 합니다. context free grammar라면 언어는 정규 파서로 파싱할 수 있습니다. 

### 2) **Parser - Lexer combination(파서-어휘 분석기 조합)**
파싱은 어휘 분석과 구문 분석 두가지로 구분할 수 있습니다. 

[1] 어휘 분석 : 자료를 토큰으로 분해하는 과정
- 공백과 같은 의미없는 문자 제거

[2] 구문 분석 : 언어의 구문 규칙을 적용하는 과정
- 언어 구문 규칙에 따라 문서 구조 분석

{{< figure src="/images/fullstack_boostcourse/web_boostcourse_2/2.png">}}

파싱 과정은 반복됩니다. 
파서는 보통 어휘 분석기로부터 새 토큰을 받아서 구문 규칙과 일치하는지 확인합니다. 

- 규칙에 맞으면
    - 토큰에 해당하는 노드가 파싱 트리에 추가 & 파서는 다른 토큰 요청
- 규칙에 맞지 않으면
    - 토큰을 내부적으로 저장하고 토큰과 일치하는 규칙이 발견될 때까지 요청
    - 맞는 규칙이 없는 경우 예외로 처리하는데 이것은 문서가 유효하지 않고 구문 오류를 포함하고 있다는 것임

### 3) 변환
파서 트리는 최종 결과물이 아닙니다. 파싱은 보통 문서를 다른 양식으로 변환하는데 컴파일이 하나의 예가 됩니다. 소스 코드를 기계 코드로 만드는 컴파일러는 파싱 트리 생성 후 이를 기계 코드 문서로 변환합니다. 

{{< figure src="/images/fullstack_boostcourse/web_boostcourse_2/3.png">}}

### 4) parser의 종류
- 하향식 파서 : 구문의 상위 구조로부터 일치하는 부분을 찾음.
    - 일치하는 다른 규칙을 점진적으로 찾아내면서 표현식을 찾음.
- 상향식 파서 : 낮은 수준에서 점차 높은 수준으로 찾음.
    - 입력 값이 규칙에 맞을 때까지 찾아서 맞는 입력값을 규칙으로 바꾸는데 이 과정은 입력값의 끝까지 진행됨. 부분적으로 일치하는 표현식은 파서 스택에 쌓임.

### 5) 파서 자동 생성
파서를 생성해 줄 수 있는 도구를 파서 생성기라고 합니다. 언어에 구문 규칙 같은 문법을 부여하면 동작하는 파서를 만들어줍니다. 

## 4. HTML parser
HTML은 파싱하기 어렵고 전통적인 구문 분석이 불가능하기 때문에 context free grammar가 아닙니다. 

### 1) HTML DTD
HTML의 정의는 DTD 형식 안에 있는데 SGML 계열 언어의 정의를 이용한 것입니다. 

### 2) DOM(Document Object Model)
파싱 트리는 DOM 요소와 속성 노드의 트리로서 출력 트리가 됩니다. 이것은 HTML 문서의 객체 표현이고 외부를 향하는 자바스크립트와 같은 HTML 요소의 연결 지점입니다. 트리의 최상위 객체는 문서입니다. 

- DOM은 마크업과 1:1 관계를 갖습니다.
    - HTML과 마찬가지로 DOM은 W3C에 의해 명세([www.w3.org/DOM/DOMTR](http://www.w3.org/DOM/DOMTR)
    )가 정해져 있습니다.

```html
<html>
  <body>
   <p>Hello World</p>
   <div><img src="example.png" /></div>
  </body>
</html>
```

{{< figure src="/images/fullstack_boostcourse/web_boostcourse_2/4.png">}}

## 5. **Render tree construction**
DOM 트리가 구축되는 동안 브라우저는 렌더 트리를 구축합니다. 표시해야 할 순서와 문서의 시각적인 구성 요소로써 올바른 순서로 내용을 그려낼 수 있도록 하기 위한 목적입니다. 

- firefox - frames
- webkit - renderer or render object

renderer는 자신과 자식 요소를 어떻게 배치하고 그려내야 하는지 알고 있습니다. 

### 1) DOM tree와 Render tree의 관계
렌더러는 DOM 요소에 부합하지만 1:1로 대응하는 관계는 아닙니다. 예를 들어 head 요소와 같은 비시각적 DOM 요소는 렌더 트리에 추가되지 않습니다. 또한 display:none인 요소도 나타나지 않습니다. 

## 참고 자료
[1] [[naver D2] 브라우저는 어떻게 동작하는가?](https://d2.naver.com/helloworld/59361)  
[2] [[web.dev] How browsers work](https://web.dev/howbrowserswork/)
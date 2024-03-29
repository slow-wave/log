---
title: "[blockchain] blockchain의 개념"
date: 2022-09-28T16:06:51+09:00
draft: false
tags: ["blockchain"]
categories: ["Blockchain"]
showToc: true
UseHugoToc: true
comments: true
---

다음은 블록체인에 대한 기본적인 개념을 이해할 수 있었던 
[But how does bitcoin actually work?](https://www.youtube.com/watch?v=bBC-nXj3Ng4) 영상을 보고 정리한 내용입니다.** 

## 블록체인을 소유하고 있는건 어떤 의미일까?

비트코인을 만든다면? 

먼저 친구들과의 금전 거래 내역을 공동의 장부에 기록한다.
장부를 믿으면 되니깐, 친구들과 세상에 대한 신뢰의 필요성은 점점 줄어든다. 
신뢰가 없어도 장부로 거래를 운영할 수 있다면 그걸 암호화 화폐라고 부르지 않을까? 

- 비트코인 = 최초의 암호화 화폐
    은행 대신에 암호학에서 탄생된 몇 가지 수학 원리를 이용해서 거래 주체들 사이에 신뢰가 필요하지 않은 똑똑한 분산 확인 시스템이 있다. 
    

- 장부(ledger)와 디지털서명(digital signature)
    돈을 주고 받을 때는 누구나 볼 수 있는 공동 장부에 기록한다. 그리고 모두 모여서 거래 내역을 보고 정산을 한다.
    
    이 시스템의 프로토콜을 간단히 정리하면 다음과 같다. 
    
    1) 누구든 장부에 기록 가능하다. 
    2) 매달 말 일에 모여서 정산한다.
    

### 문제점1. 아무나 기록할 수 있다. 공동 장부에 기록된 내용들이 돈을 보내는 사람의 의도대로 진실되게 기록되어있다는 것을 어떻게 믿을 수 있을까?

⇒ **디지털 서명** 

돈을 보낸다는 거래 기록에 서명 같은 효과를 가지는 무언가를 추가할 수 있어야한다. 물론 위조도 불가능해야한다.

- 위조를 막기위해서는 private key - public key를 생성한다.
    디지털 서명은 보통 256개의 0과 1의 조합으로 만들어진 하나의 값이다. 
    (디지털 서명의 값 = 문서의 내용 + 비밀키를 조합하는 함수에 의해 만들어짐.)
    →누군가 디지털 서명값을 훔쳐도 다른 문서에 대한 증표로는 사용될 수 없음.
    

- 디지털 서명과 관련된 두번째 함수는 디지털 서명값이 유효한지 확인하는데 사용된다. 두번째 함수는 단순하게 참 또는 거짓만을 결과값으로 반환한다.
    비밀키를 모른다면 유효한 디지털 서명값을 찾아낼 수 없다. 비밀키를 모르면 아무 값이나 디지털 서명값으로 정하고, 공개키와 함께 계산해서 디지털 서명값이 유효한지 확인하는 것을 반복하는 것 외에는 방법이 없다. 
    이 경우의 수는 2^256 개이므로 디지털 서명값이 유효하다고 확인되는 것은 충분히 확신해도 된다.
    

### 문제점2. 디지털 서명값을 위조할 수는 없지만, 동일한 거래 내역 자체는 여러번 복사할 수 있다.

→ 메시지에 거래별로 유일한 식별자인 ID값을 포함해야한다. 그렇게하면 메시지 내용이 다르므로 디지털 서명값도 달라져 복사해도 다른 곳에 쓸 수 없게 된다. 

### 문제점3. 어떤 사람이 수천 달러 지불한다는 거래기록을 장부에 남기고 먹튀를 한다면?

→ 자기들이 미리 적립해둔 금액 이상으로는 지출할 수 없게 한다. 
→ 그 시점까지의 거래 내역 전부를 알아야만 한다는 제약이 생긴다. 

장부는 해당 통화의 모든 거래 이력만을 가지고 있을 뿐, 환전을 기록하지는 않는다. 비트코인에서도 사람들이 현금으로 비트코인을 사더라도, 장부에 실질적인 돈이 들어오는 것은 아니다. 

- 장부에 새로운 내용을 추가하는 것도 중앙의 누군가가 통제하는 걸까?
중앙의 누군가에게 의지하지 않으려면 모든 사람들이 장부의 복사본을 가지고 있어야된다. 장부를 가지고 있는 모든 사람들에게 거래를 알려야한다.  

### 문제점4. 수많은 장부 중에서 어떤 장부가 올바른 장부인지를 어떻게 알 수 있을까? 우리 뿐만아니라 다른 모든 사람들도 똑같은 거래를 똑같은 순서로 그들의 장부에 기록해서 모든 사람들의 장부 내용이 언제나 완전히 같다는 것을 확신할 수 있을까?

→ 가장 많은 계산 작업을 포함하는 장부가 신뢰할만한 좋은 장부다. (암호화 해쉬함수)
무엇을 신뢰할 것인가를 판단하는 기준을 계산 작업에 둔다면, 거래 내역을 속이거나 장부의 충돌을 막기위해서는 정해진 시간 내에 처리가 불가능할만큼 엄청난 양의 계산이 필요하게 만들 수 있다.

매직넘버를 찾아내는 일은 엄청난 계산이 필요하지만 매직 넘버가 맞는 값인지 확인하는 것은 별로 많은 양의 계산이 필요하지 않다.

→ 작업 증명 (PoW)

비트코인 논문의 핵심 아이디어는 장부를 가진 모든 사람들은 가장 많은 계산 작업이 포함된 장부를 믿으면 된다는 것이다. 장부는 블록 단위로 나누어서 구성되어있고, 블록에는 일정한 수의 거래 내역이 작업증명(매직넘버)과 함께 담겨 있으며 그 블록의 해쉬값은 정해진 만큼의 여러 자리수의 0으로 시작한다. 

거래는 지불하는 송금자의 디지털 서명이 있어야만 유효한 거래로 인식되는 것처럼 블록도 마찬가지로 작업 증명이 있어야만 유효한 블록으로 인식될 수 있다. 더 나아가서 블록들의 순서까지 보장하려면 각 블록이 자기보다 앞에있는 이전 블록의 해쉬값을 포함하게하면된다. 

→ 블록은 이렇게 앞의 블록의 해쉬값을 통해 앞뒤가 연쇄적으로 연결되어있기 때문에 그냥 장부라고 부르지 않고 블록체인이라고 한다. 

체인을 이루도록 변경된 규약에 의해, 이제는 누구든지 블록을 생성할 수 있게되었다. 누구든지 브로드캐스팅 되는 거래 정보를 접수하고 접수한 거래 정보를 모아서 블록을 만들고 그 블록의 해쉬값이 60개의 0으로 시작하게하는 매직 넘버를 찾기 위해 엄청난 양의 계산작업을 수행하고 마침내 그 매직 넘버를 찾아내서 그 블록을 브로드캐스팅하면 블록의 생성자가 된다.

- 블록 보상
블록을 생성하고 보상을 받는 것을 채굴이라고 한다. 채굴자들이 실제로 하는 일은 브로드캐스팅 되는 거래 정보를 수집하고, 블록을 새로 생성해서, 생성한 새 블록을 다시 브로드캐스팅하고 보상을 받는 일이다.
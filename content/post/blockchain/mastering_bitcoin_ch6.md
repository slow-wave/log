---
title: "[blockchain] ch6. Transactions"
date: 2022-10-02T23:11:22+09:00
draft: false
tags: ["blockchain","bitcoin"]
categories: ["blockchain"]
showToc: true
UseHugoToc: true
comments: true
---

### 용어 정리
1) coinbase : 각 블록의 첫번째 트랜잭션이며 마이닝을 위한 보상.
2) locking script : 출력에 배치되는 지출 조건.
3) unlocking script : 출력을 사용할 수 있도록 하는 스크립트
4) P2PKH : 비트코인 내에서 가장 일반적인 스크립트 형식으로 거래 유형이다.
5) digital signature : 개인키와 공개키암호를 이용한 전자서명이다. 이를 통해 본인임을 인증하고 수신인은 메세지가 위,변조 되지 않았음을 확인한다.
6) SIGHASH : 데이터 가운데 어떤 부분 확인하고 서명해야하는지 지정해준다.

### **Transaction outputs**
Transaction output은 불연속적이고 분리할 수 없다는 특징을 갖고 있다. vout이라는 array에 존재한다.
- Output의 구성요소
    1) 비트코인 양 (사토시로 표시한다.)

    2) locking script (비용 지출에 필요한 조건을 결정하는 암호 퍼즐)
    

### **Transaction inputs**
Transaction input은 어떤 UTXO가 소비될지 식별하며 잠근 해제 스크립트를 통해 소유권 증빙을 제공한다.

- input의 구성요소
    1) transaction id – 사용하고자하는 UTXO가 들어있는 transaction number
    2) vout - 해당 transaction에서 몇번째 output ?
    3) scriptSig(unlocking script) – 디지털 서명과 비트코인의 소유권을 증명하는 공개키
    4) sequence number
    

### **Transaction Serialization outputs & inputs**
Serialization은 데이터 구조의 내부 표현을 한번에 1byte만큼 전송할 수 있는 형식으로 변환하는 과정이다.

- [output]
    - Amount(8bytes)  
    - locking script size(1-9bytes)  
    - locking-script(variable)  
- [input]  
    - Transaction Hash(32bytes)  
    - output index(4bytes)  
    - unlocking-script size(1-9bytes)  
    - unlocking-script(variable)  
    - sequence number(4bytes)  

### **Transaction fees**
- 대부분의 거래에는 거래 수수료가 포함되어있다. 거래 수수료는 거래를 다음 블록에 포함시키기위한 보상으로 작용하며, 또 모든 거래에 적은 비용을 부과하면서 시스템이 남용되는 것을 막는다.
- 수수료는 Transaction의 크기에 기반해 계산된다.
- 수수료가 높으면 블록에 빠르게 올라간다. (수수료는 우선순위에 영향을 미친다.)
- 수수료 추정 알고리즘은 경쟁 거래에 의해 제공되는 용량과 수수료에 기초해 계산
- 수수료 = 입력 – 출력

### **Transaction scripts and script language**
- UTXO에 배치된 잠금 스크립트와 잠금 해제 스크립트는 모두 script 언어로 작성된다. 트랜잭션이 검증되면 각 입력의 잠금 해제 스크립트가 해당 잠금 스크립트와 함께 실행되어 지출 조건을 충족하는지 확인한다.
- Turing incompleteness
    - 블록에 조건문, 반복문 등 복잡하게 돌아가는 코드를 포함시키지 않음.
- Stateless verification
    - 스크립트를 실행하는데 필요한 모든 정보가 스크립트 내에 포함되어있음.

### **Pay-to-Public-Key-Hash (P2PKH)**
- 비트코인 네트워크에서 처리되는 거래는 P2PKH로 출력된다. 이러한 출력안에는 일반적으로 비트코인 주소로 알려진 공개키 해시의 출력을 잠그는 잠금 스크립트가 포함되어있다. P2PKH 스크립트로 잠긴 출력은 해당 개인키로 만든 공개키와 디지털 서명을 제시하면 잠금 해제 할 수 있다. 실행 시 잠금 해제 스크립트가 잠금 스크립트로 설정된 조건과 일치하는 경우에만 인정한다.

### **Digital Signatures(ECDSA)**
- ECDSA는 디지털 서명 알고리즘이다. (Eliptic Curve Cryptography 기반)
- 기능
    (1) 서명은 자신을 증명하는 것이며 자금의 소유자가 지출을 승인함을 입증한다.
    
    (2) 서명을 해서 만든 트랜잭션은 내가 보내지 않았다고 부인할 수 없다.
    
    (3) 거래에 서명한 후에는 어느 누구도 수정할 수 없으며 수정할 수 없음을 입증한다.
    

### **How Digital Signatures Work**
- 디지털 서명 구성
    - Create : 메세지(transaction)와 개인키를 사용해 서명을 만드는 알고리즘
    - Verfify : 서명을 받는 사람이면 누구나 검증할 수 있는 알고리즘.
- 디지털 서명 만드는 방법
    - Fsig는 서명 sig를 생성하는데 이는 R,S로 이루어져 있다.
    - DER 방식으로 인코딩

### **Verifying the signature**
- 서명을 검증하려면 서명(R & S), serialized transaction, public key, private key가 필요하다.
- 해당 트랜잭션의 개인키 주인만 트랜잭션을 만들 수 있다.
- 유효성 검사에서는 메세지가 변형되지 않았다는 것 뿐만 아니라 valid 한지도 확인한다.

### **Signature Hash Types(SIGHASH)**
- 서명은 SIGHASH flag를 사용해 개인키로 서명된 해시에 거래 데이터의 어느 부분이 포함되는지를 나타낸다.
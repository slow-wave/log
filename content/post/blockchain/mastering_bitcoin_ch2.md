---
title: "[blockchain] ch2. How bitcoin works"
date: 2022-09-29T10:35:18+09:00
draft: false
tags: ["blockchain","bitcoin"]
categories: ["blockchain"]
showToc: true
UseHugoToc: true
comments: true
---

**Mastering Bitcoin: Programming the Open Blockchain 2nd Edition** 책을 정리한 내용입니다. 

## 용어 정리

- Decentralized : 탈중앙화. 분산된 소규모 단위로 자율적으로 운영되는 것.
- Mining : 채굴. 암호화폐의 거래내역을 기록한 블록을 생성하고 그 대가로 암호화폐를 얻는 행위.
- Peer-to-peer network : 인터넷에 연결된 다수의 개별 사용자들이 중개기관을 거치지 않고 직접 데이터를 주고받는 것.
- Consensus : 악의적인 상황이 발생하더라도 네트워크를 올바른 방향으로 이끌고자 하는 다수의 노드들이 상호 검증을 거쳐 올바른 블록 생성을 이끌어내는 프로세스와 알고리즘.
- PoW(Proof of Work) : 작업 증명. 최초의 consensus 알고리즘. 블록 생성 시간동안 가장 많은 해시파워를 제공한 노드가 블록을 생성하도록 설계.

### Introduction

비트코인은 탈중앙화된 신뢰에 기반하며 비트코인에서 신뢰는 다른 참여자들간의 상호작용으로부터 달성된다. 거래가 비트코인 시스템을 통해 추적하고 분산된 합의(distributed consensus)의 메커니즘에 의해 신뢰되고 받아들여지며 최종적으로 모든 transaction의 ledger인 blockchain에 기록되는 것을 관찰할것이다. 비트코인 시스템은 키를 포함한 지갑을 갖고 있는 users, network를 통해 전파되는 transactions, miners 로 구성된다.

### **Transaction Inputs and outputs**

각 transaction은 하나 이상의 input을 포함하고 있는데 이것은 비트코인 계정에 대한 debit과 같다. transaction의 다른 한 쪽에는 하나 이상의 output이 있는데 이것은 비트코인 계정에 추가된 credit과 같다. input(debit) & output(credit)의 양은 반드시 같지는 않다. transaction은 또한 소유권 증명(proof of ownership)을 포함한다. 비트코인에서 spending은 이전 transaction의 가치를 비트코인 주소로부터 식별된 새로운 소유자에게 이전하는 거래에 서명하는 것이다.

### **Making Change**

많은 비트코인 트랜잭션들은 새로운 소유자와 현재 소유자의 주소와 변경 주소 모두를 참조하는 output들을 포함할 것이다. 그 이유는 트랜잭션 input들은 통화 지폐와 같이 나눠질 수 없기 때문이다. 만약 주머니에서 가장 큰 지폐를 사용했다면 잔돈으로 가득찬다. 잔돈만 사용한다면 고액권만 갖게된다. 사람들은 무의식적으로 이 두 극단 사이의 균형을 찾고, 비트코인 지갑 개발자들은 이 균형을 프로그램하기 위해 노력한다. 

transaction들은 transaction input에서 transaction output으로 값을 이동한다. input은 이전 transaction의 output의 참조로, 값이 어디서 왔는지 보여준다. 가치가 소유자에서 소유자로 이동될 때 chain of ownership을 만든다.

### **Common Transaction Forms**

- 가장 일반적인 형태는 한 주소에서 다른 주소로의 지불
    - 원래 소유자에게 반환된 '변경'을 포함한다. 이런 형식의 transaction은 하나의 input과 두개의 output을 가지고 있다.
- 여러 입력을 하나의 단일 output으로 통합
- 하나의 input을 여러 수신자를 나타내는 다량의 ouput에 분배하는 거래

### **Constructing a transaction-Getting the Right inputs**

fully-node client로 운영되는 비트코인 지갑은 블록체인 상의 모든 사용되지 않은 output의 복사본을 포함한다.

### **Adding the Transaction to the ledger-Transmitting the transaction**

트랜잭션이 진행되는데 필요한 모든 정보를 가지고 있기 때문에 비트코인 네트워크에서 어떻게 그리고 어디서 전송되는지는 상관할 바가 아니다. 

비트코인 네트워크는 peer-to-peer 네트워크이다. 비트코인 네트워크의 목적은 거래와 블록을 모든 참가자들에게 전파하는 것이다.

### **How it propagates**

비트코인 네트워크에 참여하는 모든 시스템은 비트코인 노드라고 한다. 이전에 보지 못한 유효한 트랜잭션을 수신하는 모든 비트코인 노드는 'flooding'으로 알려진 전파기술을 통해 연결된 모든 다른 노드로 바로 포워딩된다.

### **Bitcoin Mining**

Alice의 transaction은 비트코인 네트워크에서 전파되고 있다. mining이라고 불리는 과정을 통해 검증되고 블럭에 포함되기 전까지는 블록체인의 일부가 되지 않은 것이다. 트랜잭션은 블록으로 번들되는데, 이를 증명하려면 엄청난 양의 계산이 필요하지만 입증된 대로 검증하는데는 적은 양의 연산만 필요하다.

mining 과정은 비트코인에서 두가지 목적으로 사용된다.

1) mining nodes가 비트코인의 'consensus rules(합의 규칙)'을 참조하여 모든 거래들을 검증한다. 
- mining은 유효하지 않거나 잘못된 거래들을 거절함으로써 비트코인 거래들에 보안을 제공한다.

2) 거의 중앙 은행이 새로운 돈을 발행하는 것과 같이 각 블록에 새로운 비트코인을 만든다. 
- 블록당 생성되는 비트코인의 양은 제한되어있으며, 정해진 발행 일정에 따라 시간이 지남에 따라 감소한다.

mining은 비용과 보상 사이에서 좋은 균형을 이룬다. 성공적인 miner는 새로운 비트코인과 거래 수수료의 형태로 보상을 수집할 것이다. 그러나 보상은 miner가 합의 규칙을 만족시키기 위해 모든 거래를 올바르게 검증한 경우에만 수집될 것이다. 이 사소한 균형은 중앙의 권위없이 비트코인의 보안을 제공한다.

비트코인에 사용되는 '퍼즐'은 암호화 그래픽 해시를 기반으로 하며, 비대칭적으로 해결하기 어렵지만 검증하기 쉽고, 난이도 조정이 가능하다는 유사한 특징을 보인다. PoW를 위한 알고리즘은 솔루션이 사전결정된 패턴과 일치하는 솔루션이 나타날 때까지 SHA256 암호화 그래픽 알고리즘으로 블록 헤더와 난수를 반복 해시하는 것을 포함한다. 이러한 솔루션을 최초로 발견한 마이너는 경쟁에서 이겨 해당 블록을 블록체인에 게시한다. 더 많은 miner들이 비트코인 네트워크에 참여하기시작할수록 문제의 어려움은 급격히 증가한다.

### **Mining Transactions in Blocks**

마이너는 새 블록을 구성할 때 이 풀에서나온 비식별된 거래들을 새로운 블럭에게 더하고 PoW 마이닝 알고리즘을 활용해 새로운 블럭의 유효성을 검증한다.

### **Spending the Transaction**

각 비트코인 클라이언트는 거래가 유효하고 사용가능한지를 확인할 수 있다. lightweight 클라이언트들은 거래가 블록체인에 있고 그 후에 채굴된 여러 블록을 가지고 있다는 것을 확인함으로써 소위말하는 단순결제검증(Simplified Payment Verification)을 수행할 수 있으며, 채굴자들이 이를 유효하다고 보장할 수있다.

### 출처

- [해시넷](http://www.hash.kr/)
- [책] **Mastering Bitcoin: Programming the Open Blockchain 2nd Edition**
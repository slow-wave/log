---
title: "[blockchain] ch5.Wallets"
date: 2022-10-01T16:21:44+09:00
draft: false
tags: ["blockchain","bitcoin"]
categories: ["blockchain"]
showToc: true
UseHugoToc: true
comments: true
---

**Mastering Bitcoin: Programming the Open Blockchain 2nd Edition** 책을 정리한 내용입니다. 

### 용어 정리
- Mnemonic codes word : Mnemonic code word는 Deterministic wallet을 도출하기위해 seed로 사용되는 임의의 숫자를 나타내는 단어 순서임. 랜덤 숫자에 비해 읽고 쓰기 쉬움.
- HD wallet : 비트코인의 BIP32표준으로 정의되어 현재 가장 발전된 형태의 지갑임. 하나의 마스터 시드 키에서 다수의 지갑을 생성할 수 있어 편리함.

### Nondeterministic Wallet (JBOK wallet, just a bunch of keys)
- 지갑 안에 있는 키 끼리 연관성 없음.
- 비트코인 주소가 필요할 때마다 개인키를 랜덤으로 만들어내며 지갑을 주기적으로 백업해야함. 이는 비효율적이므로 테스트 이외의 용도로는 권장되지 않음.

### Deterministic Wallet
- 지갑 안에 있는 모든 키는 서로 관련 있음.
- 개인키는 One-way hash function을 이용해 하나의 seed에서 파생됨.
- Seed는 BPI-39로 정의되는 Mnemonic code로 부터 만들어짐. Seed는 랜덤으로 생성된 숫자이고 인덱스 번호나 chain code와 결합되어 개인키를 도출함.
- Seed가 있으면 파생된 모든 키를 복구 할 수 있기 때문에 1번만 백업하면 됨.

### HD Wallets (BIP-32/BIP-44)
- Deterministic wallet의 발전된 한 형태임. BIP-32 표준에 의해 정의되고 single root seed로부터 생성됨.
- 트리 구조이며 트리 구조의 어떤 브랜치에는 성격을 부여할 수 있음.
- CKD(child key derivation) 함수는 one-hash 함수에 기반하는데 child key를 만들기위해서는 다음의 것이 필요함.  
    (1) 부모의 개인키나 공개키   
    (2) chain code라고 불리는 seed(256bits)   
    (3) index number(32bits)  
- Chain code는 index와 자식키를 아는 것 만으로는 다른 자식 키를 도출할 수 없도록하는데 사용됨. 초기 chain code는 시드로부터 만들어지지만 후속 자식 chain code는 각 부모 chain code로 부터 파생됨.
- 개인키를 공개하지 않아도 extended public key로부터 많은 공개키를 만들 수 있음.
- depth가 무한대로 가면 트리 탐색이 어려워짐. 특별한 키를 관리하도록 키에 의미를 부여해줌.
    - BPI-43 : 트리 구조의 목적을 나타내는 특수 식별자를 first hardened child index로 사용함.
    - BPI-44 : m / purpose’ / coin_type’ / account’ / change / address_index

### Extended keys
- 확장키는 Key(개인키 or 공개키)와 chain code를 결합한 것임. Base58check를 이용해 인코딩됨.
- Xpub로 시작하는 키는 웹페이지에 공개될 수 있음.

### Hardened child key derivation
- Xpub은 chain code를 포함하므로, 자식 개인키가 유출될 경우 chain code와 함께 사용하여 다른 자식 키들을 모두 유도할 수 있음.
- 이런 위험에 대처하기위해 부모 공개키와 자식 체인 코드 사이의 관계를 끊는 hardened derivation 함수를 사용함.
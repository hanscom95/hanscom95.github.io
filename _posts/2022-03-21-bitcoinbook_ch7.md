---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch7"                    # (require) a string title
date: 2022-03-21 12:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch7 트랜잭션 및 고급 스크립트
이번 장은 앞장에서 다뤘던 내용들의 복잡한 스크립트들을 다룬다.

## 1. 서론
다중서명을 요구하는 스크립트를 이해 하며 다중서명의 문제를 해결하기 위한 P2SH ㄴ


## 2. 다중서명(Multisignature)
* 이름 그대로 다중서명을 요구하는 스크립트
* 잠금에 N개의 공개키가 사용되고, 그 중에 적어도 M개의 서명이 올바르게 제공될 때 잠금이 해제되는 스크립트로 M-of-N으로 표현
  ```
  M  <Public Key 1> <Public Key 2> ... <Public Key N> N CHECKMULTISIG
  ```
* 해제 스크립트는 다음과 같은 형식
  ```
  <Signature 1> <Signature 2> ... <Signature M>
  ```
* CHECKMULTISIG 구현에는 버그 있음
* M개를 스택에서 꺼내야 하는데 M+1 개를 꺼냄. 이 버그 때문에 연결된 스크립트의 맨 앞에 의미 없는 0을 추가
  ```
  0 <Signature 1> <Signature 2> ... <Signature M>
  ```
* ex) 3개의 공개키 그 중 두 개 이상의 올바른 서명 스크립트는 2-of-3
  ```
  0 <Signature B> <Signature C> 2 <Public Key A> <Public Key B> <Public Key C> 3 CHECKMULTISIG
  ```
  
* 잠금 스크립트는 출력에 배치되는 지출 조건
* 잠금 스크립트는 공개키 또는 비트코인 주소(공개키 해시)가 포함되어 있어 scriptPubKey라 불림
* 잠금 해제 스크립트는 잠금 스크립트에 의해 출력에 설정된 조건을 해결하거나 만족시키는 스크립트로 출력을 사용
* 잠금 해제 스크립트는 모든 트랜잭션 입력의 일부
* 대개의 경우 개인키로 사용잔의 지갑에서 생성된 **디지털 서명**이 있음. scriptSig라 불림

## 3. Pay-to-Script-Hash(P2SH)
* 복잡한 튼랜잭션 스크립트 사용을 좀 더 간단하게 할 수 있도록 2012년에 새롭게 도입
* 다중서명 스크립트의 문제를 해결
* M-of-N 다중서명 스크립트 작성방법은 여러 가지 문제중 가장 중요한 트랜잭션 출력의 잠금 스크립트에 N개의 공개키 해시를 가져야 한다는 점.   
  비트코인은 트랜잭션 출력이 사용되기 전까지 UTXO로 관리, UTXO는 램 메모리를 사용하는 비용이 높음

P2SH가 없는 복잡한 스크립트  
|  Locking Script  | 2 PubKey1 PubKey2 PubKey3 PubKey4 PubKey5 5 CHECKMULTISIG |      
|  Unlocking Script  |  0 Sig1 Sig2  |  

P2SH가 있는 복잡한 스크립트      
|  Redeem Script  |  2 PubKey1 PubKey2 PubKey3 PubKey4 PubKey5 5 CHECKMULTISIG  |  
|  Locking Script  |  HASH160 <20-byte hash of redeem script> EQUA L |   
|  Unlocking Script  |  0 Sig1 Sig2 <redeem script>  |   

* P2SH는 공개키 해시 값들을 잠금 스크립트에 포함하지 않고, 별도 스크립트(Redeem Script)로 작성하고 해시 값만을 포함하게 함

## 3.1. P2SH의 이점
* 복잡한 스크립트를 트랜잭션 출력에서 더 짧은 스크립트로 바뀌어 트랜잭션 크기를 작게 만듬
* 스크립트는 비트코인 주소로 코딩될 수 있음, 발시자와 살신자의 지갑에는 P2SH 구현을 위한 복잡한 엔지니어링 필요치 않음
* 스크립트를 작성하는 부담을 보낸 사람이 아닌 받는 사람에게 이동
* 긴 스크립트의 데이터 저장에 대한 부담을 출력(블록체인에 저장된 것은 UTXO 세트에 있음)에서 입력(블록체인에만 저장됨)으로 이동
* 긴 스크립트의 데이터 저장에 대한 부담을 현재 시간에서 미래 시간으로 이동
* 긴 스크립트의 거래 수수료 비용을 보낸 사람에게 받는 사람에게 옮김. 긴 스크립트를 사용하려면 받는 사람에게 보내야 함


## 4. 타임락(Timelocks)
트랜잭션의 시간을 기준으로 잠궈두는 것. 트랜잭션을 기준으로 적용되는 nLockTime 필드로 구현    
이중지불 문제가 발생할 수 있기 때문에 비트코인 시스템 초기부터 있어 왔지만 거의 사용되지 않음  
잠겨 있는 트랜잭션은 특정 시간이 되기 전까지는 트랜잭션을 전송받은 노드에서 유효하지 않은 것이 되어 비트콩니 네트워크로 전파되지 않음. 
따라서 잠금 시간 전까지는 제약사항이 비트코인 시스템 밖에서만 존재하고 비트코인 시스템 내에서는 존재하지 않음
* 상대적 잠금시간
  * 트랜잭션 수준이나 스크립트 수준으로 적용 가능
  * 트랜잭션 수준의 타임락은 트랜잭션 입력마다 작성 가능하며, 트랜잭션 입력 필드 nSequence 로 작성
  * nSequence 는 메모리풀에 있는 트랜잭션을 수정할 수 있도록 하기 위한 필드
  * 값이 0xFFFFFFFF 이면 채굴자들은 블록에 트랜잭션을 포함
  * CHECKSEQUENCEVERIFY 등장으로 사용
* nSequence를 사용한 상대적 잠금시간  
  ![그림1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch7_1.png)  
  그림1 nSequence encoding의 BIP-68 정의
  * 2^31보다 작으면 상대 타임락을 갖는 것으로 해석, 그렇지 않으면 상대 타임락이 적용되지 않음
  * 타임 플래그를 사용해서 블록높이를 사용할 것인지 유닉스 타임스탬프를 사용할 것인지를 정함
  * 타임플래그는 23번째 비트 값으로 설정
  * nSequence 값은 16비트로
  * 최상위 비트 값은 nSequence 를 적용할 것인지에 대한 여부를 나타냄
  * nLockTime과 마찬가지로 nSequence값도 CHECKSEQUENCEVERIFY 입력 값보다 크거나 같아야 함
* Median-Time-Past  
  * 비트코인 시스템에서 시간의 정확성에 대해서 생각할 때는 비트코인 시스템이 P2P 네트워크라는 것에 주의
  * 채굴자에 작성하는 점에도 주의
  * BIP-113은 P2P 네트워크에서 발생할 수 밖에 없는 시간 정확성 문제를 해결하기 위해 Median-Time-Past 방법을 정의
  * 최근 11개의 블록들의 타임스탬프를 가지고 중간값을 구하고, 그 값을 시간 제약과 관련된 계산에 사용
  
  

## 5. 흐름제어기능을 가진 스크립트
* 비트코인 스크립트의 조건부 절을 흐름제어라 불림. IF, THEN, ELSE 등의 구문을 사용.  
* 표현식 무한 중첩 가능
* 조건부가 다른 조건을 포함할 수 있음
* 스크립트 언어 역방향

```
condition
IF
  code to run when condition is true
ELSE
  code to run when condition is false
ENDIF
code to run in either case
```


## ○ 참고문서
* [bitcoinbook](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch07.asciidoc)
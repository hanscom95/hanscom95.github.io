---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch10"                    # (require) a string title
date: 2022-05-31 15:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch10 채굴과 합의

## 1. 서론
### 채굴
* 분산화된 클리어링하우스의 주요 프로세스로 거래를 검증하고 승인
  * 클리어링하우스
    금융 시장에서 구매자와 판매자 사이에 지정된 중개자로서 거래를 확인하고 완료하여 구매자와 판매자 모두 계약상의 의무를 준수하도록 한다.
    (http://fnwiki.org/clearing-house/)
* 비트코인 시스템을 안전하게 보호하고 전 네트워크에 걸쳐 중앙 권력 없이 합의를 이룰 수 있도록 해 줌
* 새로운 비트코인을 생성하는 것이 아니라 인센티브 시스템일 뿐임
* 비트코인으 보안이 분산화 되도록 하는 매커니즘
### 보상
* 새 블록 각각에서 새로운 코인을 생성
* 해당 블록 내에 들어 있는 거래 전부로부터 거래 수수료를 받음
### 작업증명
* 암호화 해시를 기반으로 하는 어려운 수학 문제를 풀기 위해 경쟁
* 수학 문제에 대한 해답을 작업증명이라고 함
* 비트코인 보안 모델의 근간
### 신규 비트코인 공급 반감기
* 약 4년마다, 210,000 블록

### 채굴 노드
* 비트코인 네트워크 상에서 몇몇 노드들은 채굴자라고 불리는 특수 노드들
* 몇몇 채굴자들은 풀 노드 없이 채굴 작업을 하기도 함

## 2. 블록에 거래 추가하기
* 비트코인 노드는 검증된 거래들을 메모리 풀(memory pool) 또는 거래 풀에 추가  
* 거래 풀  
  - 거래들이 블록 내에 포함(채굴)될 수 있을 때까지 기다리는 장소
* 후보 블록(cadidate block)
  - 메모리 풀에서 작업 증명이 끝난 거래들을 삭제하고 남은 미승인 거래들로 만들어진 새로운 블록
  - 아직 유효한 작업 증명를 담고 있지 않아서 유효한 블록이 아닌 것
  - 채굴자가 작업 증명 알고리즘에 대한 솔루션을 찾는 데 성공한 경우에만 유효
* 코인베이스 거래
  - 블록에 있는 첫 번째 거래
  - 채굴 노력에 대한 보상
  - 입력값으로 UTXO를 쓰지(소비하지) 않고 무에서 비트코인을 생성해 내는 coinbase라고 불리는 하나의 입력값을 보유
  - 코인베이스가 가지는 하나의 출력값은 채굴자 자신의 비트코인 주소로 지불 가능
* 코인베이스 거래
```
$ bitcoin-cli getrawtransaction d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f 1
{
    "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0f03443b0403858402062f503253482fffffffff0110c08d9500000000232102aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21ac00000000",
    "txid" : "d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "coinbase" : "03443b0403858402062f503253482f", ⬅︎ 입력에는 다른 거래의 UTXO가 아닌 “coinbase”
            "sequence" : 4294967295
        }
    ],
    "vout" : [
        {
            "value" : 25.09094928,
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "02aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21OP_CHECKSIG",
                "hex" : "2102aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21ac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [
                    "1MxTkeEP2PmHSMze5tUZ1hAV3YTKu2Gh1N" ⬅︎ 출력에는 채굴자의 비트코인 주소
                ]
            }
        }
    ]
}
```
* 코인베이스에 대한 보상금과 수수료
  - Total Fees = Sum(Input) - Sum(Output)
  - 초기 보상금은 50억 사토시에서부터 반감기마다 시프트(“ >> 1” = “ / 2”)
  

## 3. 블록 채굴하기
* 채굴 해시 함수
  - SHA256
  - 해시(hash) = 다이제스트(digest)
* 채굴
  - 블록 헤더를 반복적으로 해싱해서 해시 결괏값이 특정 목푯값과 일치할 때까지 하나의 매개변수를 변화시키는 과정
  - 해시 함수의 결과는 미리 정하거나 생성될 수 없음
  - 답을 얻는 유일한 방법은 계속 도전해서 우연히 일치하도록 입력값을 임의로 수정
* 목푯값
  - 메모리 풀에서 작업 증명이 끝난 거래들을 삭제하고 남은 미승인 거래들로 만들어진 새로운 블록
  - 아직 유효한 작업 증명를 담고 있지 않아서 유효한 블록이 아닌 것
  - 채굴자가 작업 증명 알고리즘에 대한 솔루션을 찾는 데 성공한 경우에만 유효
* 작업 증명
  - 목푯값 이하의 해시를 생산하는 과정
  - 목푯값과 난이도는 반비례 관계

```
$ vi hash_example.py

# example of iterating a nonce in a hashing algorithm's input

from __future__ import print_function
import hashlib

text = "I am Satoshi Nakamoto"

# iterate nonce from 0 to 19
for nonce in range(20):

    # add the nonce to the end of the text
    input_data = text + str(nonce)

    # calculate the SHA-256 hash of the input (text+nonce)
    hash_data = hashlib.sha256(input_data.encode()).hexdigest()

    # show the input and hash result
    print(input_data, '=>', hash_data)

```

```
$ python3 hash_example.py
I am Satoshi Nakamoto1 => f7bc9a6304a4647bb41241a677b5345fe3cd30db882c8281cf24fbb7645b6240
I am Satoshi Nakamoto2 => ea758a8134b115298a1583ffb80ae62939a2d086273ef5a7b14fbfe7fb8a799e
I am Satoshi Nakamoto3 => bfa9779618ff072c903d773de30c99bd6e2fd70bb8f2cbb929400e0976a5c6f4
I am Satoshi Nakamoto4 => bce8564de9a83c18c31944a66bde992ff1a77513f888e91c185bd08ab9c831d5
I am Satoshi Nakamoto5 => eb362c3cf3479be0a97a20163589038e4dbead49f915e96e8f983f99efa3ef0a
I am Satoshi Nakamoto6 => 4a2fd48e3be420d0d28e202360cfbaba410beddeebb8ec07a669cd8928a8ba0e
I am Satoshi Nakamoto7 => 790b5a1349a5f2b909bf74d0d166b17a333c7fd80c0f0eeabf29c4564ada8351
I am Satoshi Nakamoto8 => 702c45e5b15aa54b625d68dd947f1597b1fa571d00ac6c3dedfa499f425e7369
I am Satoshi Nakamoto9 => 7007cf7dd40f5e933cd89fff5b791ff0614d9c6017fbe831d63d392583564f74
I am Satoshi Nakamoto10 => c2f38c81992f4614206a21537bd634af717896430ff1de6fc1ee44a949737705
I am Satoshi Nakamoto11 => 7045da6ed8a914690f087690e1e8d662cf9e56f76b445d9dc99c68354c83c102
I am Satoshi Nakamoto12 => 60f01db30c1a0d4cbce2b4b22e88b9b93f58f10555a8f0f4f5da97c3926981c0
I am Satoshi Nakamoto13 => 0ebc56d59a34f5082aaef3d66b37a661696c2b618e62432727216ba9531041a5
I am Satoshi Nakamoto14 => 27ead1ca85da66981fd9da01a8c6816f54cfa0d4834e68a3e2a5477e865164c4
I am Satoshi Nakamoto15 => 394809fb809c5f83ce97ab554a2812cd901d3b164ae93492d5718e15006b1db2
I am Satoshi Nakamoto16 => 8fa4992219df33f50834465d30474298a7d5ec7c7418e642ba6eae6a7b3785b7
I am Satoshi Nakamoto17 => dca9b8b4f8d8e1521fa4eaa46f4f0cdf9ae0e6939477e1c6d89442b121b8a58e
I am Satoshi Nakamoto18 => 9989a401b2a3a318b01e9ca9a22b0f39d82e48bb51e0d324aaa44ecaba836252
I am Satoshi Nakamoto19 => cda56022ecb5b67b2bc93a2d764e75fc6ec6e6e79ff6c39e21d03b45aa5b303a
```
* 작업 증명
  - 목푯값 이하인 해시를 생성한 13번째 실행 결과가 작업 증명 결과임, 승리의 난스 값은 13
  - 0x0ebc56d59a34f5082aaef3d66b37a661696c2b618e62432727216ba9531041a5 ≦ 0x1000000000000000000000000000000000000000000000000000000000000000





## 4 블록 채굴하기 
* ex) 10-3의 277316 블록
```
$ bitcoin-cli getblockhash 277316
0000000000000001b6b9a13b095e96db41c4a928b97ef2d944a9b31b2cc7bdc4

$ bitcoin-cli getblock 0000000000000001b6b9a13b095e96db41c4a928b97ef2d944a9b31b2cc7bdc4
{
    "hash" : "0000000000000001b6b9a13b095e96db41c4a928b97ef2d944a9b31b2cc7bdc4",
    "confirmations" : 35561,
    "size" : 218629,
    "height" : 277316,
    "version" : 2,
    "merkleroot" : "c91c008c26e50763e9f548bb8b2fc323735f73577effbc55502c51eb4cc7cf2e",
    "tx" : [
        "d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f",
        "b268b45c59b39d759614757718b9918caf0ba9d97c56f3b91956ff877c503fbe",

        ... 417 more transactions ...

       ],
    "time" : 1388185914,
    "nonce" : 924591752, # 난스
    "bits" : "1903a30c", #︎ 목푯값(bits 또는 target bits)
    "difficulty" : 1180923195.25802612, # 난이도
    "chainwork" : "000000000000000000000000000000000000000000000934695e92aaf53afa1a",
    "previousblockhash" : "0000000000000002a7bbd25a417c0374cc55261021e8a9ca74442b01284f0569"
}
```
* 목푯값: 0x1903a30c, 난스: 924591752, 난이도: 1180923195.25802612


## 5 블록을 성공적으로 채굴하기

1. 후보 블록을 구성하여 채굴 준비
2. 채굴 노드에 연결
3. 채굴 노드가 블록 헤더를 채굴기로 전송
4. 채굴기 중 한 대가 솔루션 해결
5. 솔루션 결과를 채굴 노드에 전송
6. 채굴 노드에서 솔루션 검증
7. 이웃 노드들에게 전송
8. 검증 후 다른 이웃 노드들에게 전송
9. 자신의 블록체인 복사본에 추가
10. 진행 중인 채굴을 멈추고 새로운 블록 채굴 시작
11. 위에 진행사항 반복



## 6 새 블록을 검증하기
* 새 블록 검증 기준
  - 해당 블록의 데이터 구조는 문법적으로 유효
  - 해당 블록 헤더 해시는 (작업 증명을 시행하는) 목푯값 이하
  - 해당 블록의 타임스탬프는 (시간 오류를 고려해서) 향후 2시간 이내
  - 해당 블록의 크기는 허용할 수 있는 한도 내에 있음
  - 제일 첫 거래(첫 거래에만 적용)는 코인베이스 거래
  - 블록 내에 있는 거래 전부는 거래 체크리스트를 이용해서 유효함을 판단


## 7 블록체인을 수집해서 선택하기
* 비트코인의 분산화된 합의 매커니즘의 마지막 단계는 블록을 체인 안에 모아서 가장 많은 작업 증명을 보유하고 있는 체인을 선택하는 일
* 노드가 보관하고 있는 블록
  - 메인 블록에 연결되어 있는 블록
  - 메인 블록체인에서 나와서 브랜치를 형성하는 블록(2차 체인)
      + 새로운 블록은 가끔 메인 블록이 아닌 체인을 확장
      + 2차 체인이 메인 체인보다 더 많은 누적 작업을 가지게 되는 경우 해당 노드는 2차 체인으로 재수렴
        * 메인 체인이 2차 체인이 되고 2차 체인이 메인 체인이 됨
  - 알려진 부모가 없는 블록(고아)
      + 유효한 블록을 전송받았는데 부모 블록이 현 체인에서 발견되지 않는 경우, 고아 풀에 저장
      + 부모 블록이 도착하고 기존 체인에 연결되고 나면 고아 풀에서 빠져 나와 부모 블록에 연결
      + 두 개의 블록이 각자 짧은 시간 내에 채굴되어 반대의 순서로 도착하는 경우에 발생(부모 블럭이 도착하기 전에 자식 블럭이 도착)
  - 검증에 실패한 블록은 어디에도 속하지 않음

* 합의
  - 체인 간의 임시적인 불일치를 가장 누적 작업이 많이 이루어진 유효한 체인을 선택함으로 합의가 이루어짐 
  - 채굴 파워로 ‘투표'
  
  
## ○ 참고문서
* [bitcoinbook](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch10.asciidoc)
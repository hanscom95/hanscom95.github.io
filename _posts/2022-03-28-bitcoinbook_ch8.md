---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch8"                    # (require) a string title
date: 2022-03-28 11:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch8 네트워크

## 1. 서론
8장에서 설명하는 네트워크는 비트코인에서 사용되는 노드들의 블록체인 데이터를 전송하는 방식과 방법에 관한 내용이다.
* 거래란 비트코인 시스템 내에 있는 참가자들 간 가치를 전송하는 행위를 인코딩한 데이터 구조

## 2. 거래 세부 사항
* 비트코인 어플리케이션 사용자 인터페이스에서 볼 수 있는 고급 구성 요소들은 비트코인 시스템 내에 실제로 존재하지 않는다.
> '실제로 존재하지 않거나 단일 거래에 존재하지 않는다'가 맞는 표현 같음

아래 코드는 Raw 트랜잭션을 검색하고 디코딩 내용 이다. 해당 내용으로는 코인, 발신자, 수신자, 잔액, 계정 및 주소가 없다.
```
$ bitcoin-cli getrawtransaction 7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18
0100000001524d288f25cada331c298e21995ad070e1d1a0793e818f2f7cfb5f6122ef3e71000000008c493046022100a59e516883459706ac2e6ed6a97ef9788942d3c96a0108f2699fa48d9a5725d1022100f9bb4434943e87901c0c96b5f3af4e7ba7b83e12c69b1edbfe6965f933fcd17d014104e5a0b4de6c09bd9d3f730ce56ff42657da3a7ec4798c0ace2459fb007236bc3249f70170509ed663da0300023a5de700998bfec49d4da4c66288a58374626c8dffffffff0180969800000000001976a9147f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a888ac00000000

$ bitcoin-cli decoderawtransaction 0100000001524d288f25cada331c298e21995ad070e1d1a0793e818f2f7cfb5f6122ef3e71000000008c493046022100a59e516883459706ac2e6ed6a97ef9788942d3c96a0108f2699fa48d9a5725d1022100f9bb4434943e87901c0c96b5f3af4e7ba7b83e12c69b1edbfe6965f933fcd17d014104e5a0b4de6c09bd9d3f730ce56ff42657da3a7ec4798c0ace2459fb007236bc3249f70170509ed663da0300023a5de700998bfec49d4da4c66288a58374626c8dffffffff0180969800000000001976a9147f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a888ac00000000
{
  "txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",
  "hash": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",
  "version": 1,
  "size": 225,
  "vsize": 225,
  "weight": 900,
  "locktime": 0,
  "vin": [
    {
      "txid": "713eef22615ffb7c2f8f813e79a0d1e170d05a99218e291c33daca258f284d52",
      "vout": 0,
      "scriptSig": {
        "asm": "3046022100a59e516883459706ac2e6ed6a97ef9788942d3c96a0108f2699fa48d9a5725d1022100f9bb4434943e87901c0c96b5f3af4e7ba7b83e12c69b1edbfe6965f933fcd17d[ALL] 04e5a0b4de6c09bd9d3f730ce56ff42657da3a7ec4798c0ace2459fb007236bc3249f70170509ed663da0300023a5de700998bfec49d4da4c66288a58374626c8d",
        "hex": "493046022100a59e516883459706ac2e6ed6a97ef9788942d3c96a0108f2699fa48d9a5725d1022100f9bb4434943e87901c0c96b5f3af4e7ba7b83e12c69b1edbfe6965f933fcd17d014104e5a0b4de6c09bd9d3f730ce56ff42657da3a7ec4798c0ace2459fb007236bc3249f70170509ed663da0300023a5de700998bfec49d4da4c66288a58374626c8d"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.10000000,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a9147f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a888ac",
        "address": "1Cdid9KFAaatwczBwBttQcwXYCpvK8h7FK",
        "type": "pubkeyhash"
      }
    }
  ]
}
``` 


## 3 거래 출력값과 입력값
* UTXO(Unspent Transaction output)  
  소비되지 않은 거래 출력값
* 거래  
  - 모든 거래는 UTXO세트 내의 변화(상태 변화)를 의미  
  - 아래 그림의 UTXO세트에서 점선은 UTXO세트에서 제외될 것이며 녹색 바탕은 UTXO세트로 추가
* 출력값
  - 개별적 
  - 나눌 수 없는 가치의 단위
  - 정수로 된 사토시 단위로 표현
  - 소비되지 않은 출력값을 거래에서 통째로 소비될 수만 있음
    + 잔액은 해당 거래에 출력값으로 추가되며 다른 거래의 입력값이 됨 
* 코인베이스 거래
  - 입력값과 출력값에 대한 특수한 유형의 거래
  - 각 블록 내의 첫번째 거래
  - 채굴에 성공한 채굴자에 의해 생성
  - 새 비트코인 생성
  - UTXO를 소비하지 않고 코인베이스라는 이름을 가짐
    + vin.txid 대신 vin.coinbase
  
![그림1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch6_1.png)  
그림1 블록체인을 기반으로 구축되는 Joe에서 Gopesh까지의 트랜잭션 체인

### 3.1 거래 출력값
* 거래 출력값 구성
  - 비트코인의 최소 단위인 사토시로 표현되는 비트코인 금액
  - 출력값을 소비하기 위해 필요한 조건을 결정하는 암호 퍼즐
* 암호퍼즐
  - 잠금 스크립트(locking script), 서명 스크립트(witness script), scriptPubKey
* decoderawtransaction 중 vout(위에 코드 값으로 확인)
  - value.scriptPubKey만 저장됨
  - 그 외 값은 어플리케이션에서 해석
  
|Size|Field|Description|
|----|:----:|----|
|8 bytes(little-endian)|Amount|Bitcoin value in satoshis(10^-8 bitcoin)|
|1-9 bytes(VarInt)|Locking-Script Size|Locking-Script length in bytes, to follow(실제로 1~9 bytes 중 몇 byte인지 어떻게 확인할 수 있을까?)<br>인터넷 사이트에는 10,000 bytes까지로 되어 있음|
|Variable|Locking-Script|A script defining the conditions needed to spend the output|

### 3.2 거래 입력값
* 거래 입력값 구성
  - 소비될 UTXO의 거래 ID: 해당 거래의 해시와 일련번호를 참조하여 한 UXTO를 지시
  - 해제 스크립트 : UTXO 소비 요건을 만족하기 위해 지갑을 구성하며 대부분의 해제 스크립트는 비트코인의 소유권을 증명하는 디지털 서명 및 공개키
* decoderawtransaction 중 vin
  - 거래 ID(txid): 소비될 UTXO가 담겨 있는 거래를 참조(거래에는 여러 UTXO가 있을 수 있음)
  - 출력값 인덱스(vout): 참조 거래에서 어떤 UTXO가 참조될지 식별, 참조 거래 vout배열의 인덱스, 첫번째 UTXO의 값은 0
  - 스크립트 시그(scriptSig): 참조된 UTXO의 조건을 만족하며, 소비를 위해 UTXO를 해제, 앨리스의 지갑이 참조된 UTXO를 추적하고 그에 대한 잠금 스크립틀르 점검한 후 잠금 스크립트를 이용해 필요한 해제 스크립트를 만듦
* 참조 거래의 vout 중 소비될 UTXO가 현재 거래의 vin과 연결
* 참조 거래를 getrawtransaction, decoderawtransaction한 뒤 vout을 확인
  - 입력값이 0.1 BTC임
  - 잠금 스크립트(scriptPubKey)를 가지고 있음
  
|Size|Field|Description|
|----|:----:|----|
|32 bytes(little-endian)|Transaction Hash|Pointer to the transaction containing the UTXO to be spent|
|4 bytes|Output Index|The index number of the UTXO to be spent; first one is 0|
|1-9 bytes(VarInt)|Unlocking-Script Size|Unlocking-Script length in bytes, to follow(실제로 1~9 bytes 중 몇 byte인지 어떻게 확인할 수 있을까?)<br>인터넷 사이트에는 10,000 bytes까지로 되어 있음|
|Variable|Unlocking-Script|A script that fulfills the conditions of the UTXO locking script|
|4 bytes|Sequence Number|Used for locktime or disabled (0xFFFFFFFF)|

### 3.3 거래 수수료
* 비트코인 네트워크를 안전하게 유지해 주는 역할을 하는 비트코인 채굴자들에게 제공하는 일종의 보상금
* 공격자들이 네트워크를 거래로 넘쳐나게 하는 일을 경제적 측면에서 실행 불가능하게 함으로써 그 자체로 보안 매커니즘의 역할도 함
* 비트코인 단위로 체굴되는 거래 가치보다는 킬로바이트 단위의 거래 크기를 근거로 계산
* 비트코인 내의 시장의 힘에 의해 결정
* 채굴자는 수수료 등 여러 가지 기준에 따라 거래의 우선순위를 정함
* 특정 거래에서는 무료 거래를 진행하기도 함
* 비트코인 코어에 수수료 중개(fee relay) 정책이 최소 중개거래 수수료(minrelaytxfee) 옵션에 의해 결정됨
* 거래의 데이터 구조에는 수수료 필드가 없음
* Fees = Sum(Inputs) - Sum(Outputs)
* Output에는 반드시 잔액에 대한 출력값도 포함해야 함. 그렇지 않으면 모든 잔액이 수수료로 취급

## 4 거래 스크립트와 스크립트 언어
* 스크립트 언어는 포스(Forth) 언어처럼 구성되고 역폴란드 표기법을 따르는 스택 기반의 실행 언어
* 거래가 유효화될 때 입력값 각각에 들어 있는 해제 스크립트(scriptSig)는 소비 조건을 만족하는지 여부를 알아보기 위해 대응되는 잠금 스크립트(scriptPubKey)와 함께 실행
* 최소한의 프로세싱만 지원하므로 의도적인 보안을 제공
* Pay-to-Public-Key-Hash 스크립트 기반이나 매우 다양하고 복잡한 조건을 표현하기 위해 잠금 스크립트를 사용할 수 있음
* 튜링 불완전성(Turing Incompleteness)
  - 스크립트 언어는 조건부 흐름 제어 기능 이외에는 루프나 복잡한 흐름 제어 능력을 가지고 있지 않음 -> 튜링이 완전하지 않음
* 무상태 검증(Stateless Verification)
  - 스크립트 언어는 무상태형
  - 스크립트 언어 내에서는 스크립트 실행 전 혹은 실행 후 저장되는 상태가 없음
  - 스크립트를 실행하는 데 필요한 모든 정보는 스크립트 내에 담겨 있음
  
### 4.1 잠금 스크립트(locking script)
* 출력값에 위치
* 향후 출력값을 소비하기 위해 충족되어야 하는 요건을 명시
* 역사적으로 보통 공개키 혹은 비트코인 주소(공개키 해시)가 담겨 있기 때문에 스크립트펍키(scriptPubKey)라고 부르기도 했음
* 서명 스크립트(witness script), 암호퍼즐(cryptographic puzzle)들도 추상화되어 모두 같은 개념

### 4.2 해제 스크립트(unlocking script)
* 임력값에 위치
* 잠금 스크립트가 출력값에 놓아 둔 조건을 해결하거나 충족시켜서 출력값이 소비될 수 있도록 하는 스크립트
* 대부분의 경우 사용자의 지갑이 개인키로부터 생성한 디지털 서명을 담고 있기 때문에 스크립트시그(scriptSig)라고 불림
* 해제 스크립트 전부가 서명을 포함해야 하는 것은 아님

![그림2](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch6_2.png)  
그림2 scriptSig 및 scriptPubKey를 결합하여 트랜잭션 스크립트 평가
> 거래 입력값(UTXO참조, 해제 스크립트) -> 참조 거래의 UTXO(잠금 스크립트) -> 스크립트 실행(1.해제 스크립트, 2.잠금 스크립트) -> 입력값 유효화


  
## ○ 참고문서
* [bitcoinbook](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc)
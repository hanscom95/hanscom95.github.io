---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch8"                    # (require) a string title
date: 2022-04-13 11:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch8 네트워크

## 1. 서론
8장에서 설명하는 네트워크는 비트코인에서 사용되는 노드들의 블록체인 데이터를 전송하는 방식과 방법에 관한 내용이다.
* 비트코인은 기존 금융 시스템과 달리 중앙 서버가 없는 탈 중앙화(Decentralization)네트워크
* 자율적으로 운영되며, 데이터가 한 곳에 집중되지 않고 각 노드(client)에 부산

## 2. 네트워크 구성 및 노드의 유형
* 노드의 특성
  - 비트코인 노드는 지갑 서비스, 채굴, 블록체인 데이터베이스, 네트워크 라우팅 등 기능의 집합체
  - 스마트폰과 같은 자원 제약이 있는 환경에서 실행되어야 하는 노드도 있기 때문에 노드 역할을 좀 더 다양화할 필요가 있음
  - Full Blockchain은 가장 최신의 블록체인 복사본을 가지고 있으며 외부 참조 없이도 독자적이고 신뢰할 수 있는 방법을 통해 어떠한 거래도 검증할 수 있음
  - 요즘 점점 더 많은 SPV 노드 들이 Wallet이 되어 가고 있움
    + 블록체인 전체가 아닌 블록의 헤더 정보만 가지고 있음
  - 아래 이미지가 확장 비트코인 네트워크 내에 있는 노드 중 가장 일반적인 형태
  ![그림1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_1.png)  
  그림1 비트코인 네트워크 노드 4가지 유형
* 노드의 유형
  - Reference Client
    + 가장 일반적인 형태의 노드
  - Full Block Chain Node
    + 블록체인 전체를 관리하는 노드
    + 현재까지 발생한 모든 거래내역들이 보관되어 있음
  - Solo Miner
    + 단독 마이닝 노드라고부르며, 마이닝 풀에 의존하는 마이닝 노드아 다르게 단독으로 마이닝을 진행하는 노드(채굴만을 하는 노드)
  - Lightweight(SPV) Wallet
    + 모바일과 같이 모든 정보를 가지고 있을 수 없는 환경에서 헤더 정보만을 가지고 있으며, 지갑 기능만을 수행하는 노드
  - Pool Protocol Servers
    + 마이닝 풀에 의존한느 마이닝 노드의 중앙 서버 역할을 수행하는 노드
  - Mining Nodes
    + 풀 프로토컬 서버로 부터 전달 받은 연산만을 수행하고, 연산 결과를 전달. 마이닝 풀에 의존하여 순전히 마이닝 기능만을 제공하는 노드
  - Lightweight(SPV) Stratum wallet
    + 라이트 노드와 동일하게 헤더 정보만을 가지고 있는 지갑역할을 수행하는 노드, 스트라텀 프로토콜을 사용하는 노드
  ![그림2](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_2.png)  
  그림2 확장 비트코인 네트워크상에 있는 다양한 유형의 노드들
      
![그림3](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_3.png)  
그림3 다양한 노드 유형, 케이트웨이, 프로토콜을 보여 주는 확장 비트코인 네트워크


## 3. 네트워크 검색(Network Discovery)
* 네트워크 검색 1
  - 새로운 노드를 작동시킬 때는 반드시 네트워크상에 존재하는 다른 비트코인 노드들을 검색해야만 네트워크에 참여할 수 있음
  - 지리적 위치는 중요 하지 않음
    + 비트코인 네트워크 토폴로지는 지리학적인 위치를 근거로 규정하는 것이 아님
  - 일반적인 포트 8333번(대안 포트 있을시 그쪽으로 연결)
  - TCP 커넥션
  - 핸드쉐이크 과정
    1. 연결이 될시 송신 노드가 version 메시지 전송
    2. 수신 노드는 연결을 승인할 경우 송신자에게 verack 응답
    3. 수신 노드가 양방향 연결을 원할 경우 version을 송신 노드에게 요청
    4. 송신 노드는 연결을 승인할 경우 수신자에게 verack 응답
      ![그림4](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_4.png)  
      그림4 이웃 node 사이에 초기 핸드쉐이크 과정
  - 기본적인 식별 정보
    + nVersion : 클라이언트가 구사하는 비트코인 P2P 프로토콜 버전 상수
    + nLocalServices : 노드가 지원하는 로컬 서비스 목록, NODE_NETWORK
    + nTIme : 현재시간
    + addrYou : 원격 노드의 IP주소
    + addrMe : 로컬 노드의 IP주소
    + subver : 로컬 노드에서 구동하는 소프트웨어 버전
    + BaseHeight : 로컬 노드가 알고 있는 블록체인의 블록 높이
  
* 네트워크 검색 2
  - 새로운 노드는 비트코인 클라이언트 내에서 seed node로 클라이언트 등록되어 있는 안정되게 동작하는 노드들을 이용해서 빠르게 다른 노드를 검색
  - 클라이언트를 구동할 때 종자 노드를 사용 하지 않으면 한 개 이상의 비트코인 노드 IP를 입력
  - 주소 검색 프로토콜 핸드쉐이크 과정
    1. 새로운 노드가 자신의 ip주소를 수신 받을 노드에게 전송
    2. getaddr 메시지를 수신 노드에게 전송(다른 노드들의 IP목록 요청)
    3. 자신과 연결되었던 노드들의 addr을 수신자에게 전송함
   ![그림5](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_5.png)  
   그림5 주소 검색 프로토콜
  - 새롭게 연결된 노드가 더 잘 알려지게 하여 연결성이 강해지게 됨

``` 
$ bitcoin-cli getpeerinfo
[
  {
    "id": 25, // peer index
    "addr": "82.64.20.76:8333", // peer ip address and port
    "addrbind": "172.17.0.2:42988",
    "addrlocal": "211.115.219.53:42988",
    "network": "ipv4",
    "services": "0000000000000409", // services offered 
    "servicesnames": [
      "NETWORK",
      "WITNESS",
      "NETWORK_LIMITED"
    ],
    "relaytxes": true,
    "lastsend": 1649054912, // 2022/04/04 06:48:32
    "lastrecv": 1649054912, // 2022/04/04 06:48:32
    "last_transaction": 1649054909,
    "last_block": 1648811299,
    "bytessent": 372700728, // total bytes sent 372MB
    "bytesrecv": 1219889918, // total bytes received 1219MB
    "conntime": 1644988847, // 2022/02/16 05:20:47
    "timeoffset": 70,
    "pingtime": 0.285239, // ping time
    "minping": 0.255473,
    "version": 70016, // peer version
    "subver": "/Satoshi:22.0.0/", // string version
    "inbound": false, // Outbound
    "bip152_hb_to": false,
    "bip152_hb_from": false,
    "startingheight": 723547, // starting height (block) of the peer
    "synced_headers": 730354,
    "synced_blocks": 730354,
    "inflight": [
    ],
    "addr_processed": 20807,
    "addr_rate_limited": 0,
    "permissions": [
    ],
    "minfeefilter": 0.00001000,
    "bytessent_per_msg": { // 송신된 유형별 메시지 데이터
      "addrv2": 1067717,
      "cmpctblock": 15911,
      "feefilter": 32,
      "getaddr": 24,
      "getblocktxn": 1090,
      "getdata": 60549038,
      "getheaders": 1053,
      "headers": 341108,
      "inv": 304670574,
      "notfound": 219,
      "ping": 1082464,
      "pong": 1083936,
      "sendaddrv2": 24,
      "sendcmpct": 858,
      "sendheaders": 24,
      "tx": 3886482,
      "verack": 24,
      "version": 126,
      "wtxidrelay": 24
    },
    "bytesrecv_per_msg": { // 수신된 유형별 메시지 데이터
      "addrv2": 863191,
      "blocktxn": 1838424,
      "cmpctblock": 36865068,
      "feefilter": 32,
      "getdata": 399032,
      "getheaders": 1053,
      "headers": 397500,
      "inv": 194440435,
      "notfound": 1555,
      "ping": 1083936,
      "pong": 1082464,
      "sendaddrv2": 24,
      "sendcmpct": 66,
      "sendheaders": 24,
      "tx": 982916940,
      "verack": 24,
      "version": 126,
      "wtxidrelay": 24
    },
    "connection_type": "outbound-full-relay" // default outbound-full-relay
  }…
]
``` 

## 4 Full Node, SPV Node
* Full Node  
  - 모든 트랜잭션이 포함된 전체 블록체인을 유지하는 노드
  - 풀 노드들 끼리 데이터를 동기화하여 데이터의 신뢰성과 일관성을 유지
  - 풀 노드 블록체인 동기화 핸드쉐이크 과정
    1. version 메시지로 시작(블록 높이를 알 수 있는 BestHeight 포함)
    2. version 정보를 보고 노드들의 블록을 비교 블록체인상 가장 높은 블록의 해시와 getblocks 메시지 교환
    3. inv 메시지를 송부 하여 500개의 블록 해시를 전송
    4. 해당 블록을 가지고 있지 않으면 getdata 블록을 전송
    5. 해시정보에 대한 블록 데이터 전송
   ![그림6](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_6.png)  
   그림6 Full Node 블록체인 동기화
  - 새롭게 연결된 노드가 더 잘 알려지게 하여 연결성이 강해지게 됨

* SPV Node
  - 거래에 대한 모든 블록체인 노드를 저장하지 않고도 트랜잭션을 검증하는 방법
  - 블록 헤더만을 다운로드
  - 거래내용이 없기 떄문에 블록체인의 크기는 풀 블록체인 크기보다 약 1,000배 정도 작음
  - 블록 헤더만을 갖고 있기 때문에 유효성 만을 검증할 수 있음(총 80바이트)
    + 버전(4바이트)
    + 이전 블록해시(32바이트)
    + 머클루트 블록해시(32바이트)
    + 블록 시간(4바이트)
    + nBits(4바이트)
    + nonce(4바이트)
  - 검증하기 위해 다른 노드들에게 의존
  - SPV 노드 블록 헤더 동기화 핸드쉐이크 과정
    1. getheaders 메시지를 전송
    2. header를 받아 블록 동기화(최대 2,000개 전송)
   ![그림7](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_7.png)  
   그림7 SPV 블록 헤더 동기화
  - 특정 데이터에 대해 요청할 경우 자신들의 지갑 내에 있는 주소가 유출될 수 있음
  - 보안에 취약정을 해결하기 위해 블룸필터 기능 추가



## 5 블룸필터
* SPV 노드의 프라이버시 리스크 해결하기 위해 도입
* 확률적 검색 필터
* 패턴이 무엇인지 정확하게 규정할 필요 없이 원하는 패턴을 설명하는 방식
* [BIP-37](https://github.com/bitcoin/bips/blob/master/bip-0037.mediawiki) 로 정의
* N개의 해시 함수와 M개의 1비트 배열로 구성
* N개의 해시 함수는 1에서 M사이의 출력 값을 가지며, 해당 출력값에 해당하는 인덱스는 비트배열을 1로 설정
* 출력결과는 M비트 배열을 N개가 1로 설정된 값
* N과 M을 조절함으로 정확도와 프라이버시 보호 수준을 조절
* 블룸필터 작동 방식 
  - 8비트 필드
  - 3개의 해시 함수
  - 패턴 A는 K1, K2, K3 해시 함수 입력
  - 결과 해시 값은 16개의 필드 중 하나
  - 연산 과정
    1. 비트 배열을 0으로 초기화
    2. 패턴A 과정 진행
    3. 해시 함수의 입력값을 K1=3, K2=1, K3=14
    4. 각 비트 필드의 값을 1로 설정
    ![그림8](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_8.png)  
    그림8 단순한 블룸필터 예시, 3개의 해시 함수와 16비트 필드
    ![그림9](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_9.png)  
    그림9 단순한 블룸필터의 A패턴
    5. 패턴B과정 진행
    6. 해시 함수의 입력값을 K1=16, K2=1, K3=7
    7. 각 비트 필드의 값을 1로 설정, 원래 1로 설정되 있는 값은 그대로 내버려 둠
    8. X패턴이 존재하는지 검증(존재할 가능성이 있지만 반드시 존재한다는 것은 아님)
    9. 검증 결과 maybe, yes
    ![그림10](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_10.png)  
    그림10 단순한 블룸필터의 B패턴
    ![그림11](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_11.png)  
    그림11 X 패턴이 존재하는지 검증
    10. Y패턴 과정 진행
    11. 해시 함수의 입력값을 K1=16, K2=2, K3=7
    12. Y패턴이 존재하는지 검증(존재할 가능성이 있지만 반드시 존재한다는 것은 아님)
    13. 검증 결과 K2의 2번째 필드 값이 0으로 설정 되어 있어 패턴이 확실히 일치하지 않음
    14. Y패턴 테스트 결과 확실히 아님을 의미하는 최종 부정 일치
    ![그림12](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch8_12.png)  
    그림12 Y 패턴이 존재하는지 검증
  - 패턴을 제거하는 것은 불가능하며 수정이 필요할시 0으로 초기화 후에 패턴을 다시 추가

## 6 정리
* 비트코인은 노드의 집합체를 구성하여 네트워크를 구성
* 노드가 네트워크에 접속을 하기 위해서는 최근에 연결된 노드나 시드 노드를 통하여 접속
* 네트워크 자원의 문제로 모든 노드와 계속하여 연결된 상태가 아니며 트래픽 상황에 따라 유동적으로 노드와 다시 연결
* 노드가 네트워크 상에서 특정 거래에 대한 검색을 하게 될 경우 블룸필터라는 해시 함수가 들어 있는 기능 이용, 프라이버시 유지
* 거래에 대한 출력값을 해시하여 목록으로 가지고 있으며 자기 거래 내부에 거래가 있으면 인지
* 블룸필터 내용을 다른 노드에게 전파하게 되며 다른 노드들은 같은 블룸필터를 사용하면서 거래 검색을 확인
  
## ○ 참고문서
* [bitcoinbook](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc)
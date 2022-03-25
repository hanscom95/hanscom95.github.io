---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch7"                    # (require) a string title
date: 2022-03-21 12:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch7 트랜잭션 및 고급 스크립트
이번 장은 비트코인 암호화 관련된 내용인데 암호화 관련 지식이 없어 내용을 이해하는데 어려움이 생긴다.  
수학적 내용까지 완벽히 이해 하기 어려우니 겉으로나마 진도를 빼기 위해서 다음 장을 넘어가려고 한다.     
> 암호화는 비밀을 공개하지 않고 비밀에 대한 지식을 증명하거나(디지털 서명) 데이터의 진위를 증명하는데 사용할 수도 있다.(비트코인 주소)

## 1. 서론
* 비트코인에 대한 소유권은 '디지털 키, 비트코인 주소, 디지털 서명'으로 성립  
* 디지털 키는 실제로 네트워크에 저장되는 것이 아님, 사용자가 지갑이라는 파일 또는 간단한 데이터베이스에 저장  
* 키는 개인(private)키와 공개(public)키로 구성된 쌍으로 구성  
* 비트코인 시스템은 탈중앙화된 기술로 이루어져 있음  
![그림1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch4_1.png)
그림1 개인키, 공개키 주소 변환 과정


## 2. 개인키(Private Key)
* 무작위로 추출된 숫자(randomly)
* OS 내부의 난수생성기를 이용 256비트의 난수 생성
* 16진수로 표기시 64자리
* 잃어버리면 다신 찾을 수 없다!
> 개인키는 1에서 n-1 사이의 암호화를 사용한 방법으로 생성되는 랜덤수. n은 1.158*10^77 로 2^256 보다 작은 수

### 2.1 공개키 주소 출력
bitcoin core가 출력하는 개인키는 WIF(Wallet Import Format)라 불리는 checksum을 사용하는 Base64로 인코딩 
```
$ bitcoind -regtest -daemon # 비트코인 로컬 테스트 모드 가동

$ bitcoin-cli -testnet createwallet testuser1 # tesetuser1 이라는 로컬 지갑 생성

$ bitcoin-cli -regtest getnewaddress testuser1 # 공개키 주소 출력
bcrt1qwqun64v25383jx27y2nnwrfys9wmdegrkth0s9

$ bitcoin-cli -regtest dumpprivkey bcrt1qwqun64v25383jx27y2nnwrfys9wmdegrkth0s9 # 개인키 주소 출력
cUHyunoR5DXoASGDZ8ruvVbELyqEKAErbS8Y8coZeFwiCRb3pCqw

$ ./bx seed | ./bx ec-new | ./bx ec-to-wif
KzkpWYY7QZmV2v4U3LMHj3d1DBj3DTAchzy9jVpBfV5oh2bTY2KL
```

## 3. 공개키(Public Key)
타원 곡선 곱셈을 사용하여 개인키에서 계산된다. 겉할기 수준으로 이해를 하고 넘어가자...
* bitcon core에서는 specp256k1 최적화 C라이브러리를 사용 정의
* 타원 곡선 함수는 비대칭 함수, 단방향으로는 계산이 쉽지만, 반대 방향으로의 계산은 매우 어려움
* K(공개키) = k(주의:개인키 아님) * G
  * G는 타원곡선 상의 한점(x, y)으로 정의
  * 타원 곡선 함수의 연산은 덧셈의 반복 
    * kG = G + G + G + … + G (k times)
  * 타원 곡선 그래프는 G를 포함한 접선을 그어 G가 아닌 타원 곡선 그래프 상에 임의의 접점을 구하여 x축 대칭을 시키는 과정을 반복

![그림2](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch4_2.png)
그림2 타원 곡선 함수 계산 과정

## 4. 비트코인 주소(Bitcoin Address)
단방향 암호화 해싱을 통해 공개키에서 파생된다.
SHA256 함수와 RIPEMD160 함수를 사용. 두 해시함수를 연속적으로 적용하는 함수 HASH160
생성된 공개키 해시 값을 Base58Check으로 인코딩 함수를 사용해서 주소를 생성. 이를 비트코인 주소라 한


![그림3](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch4_3.png)  
그림3 공개키에서 비트코인 주소 생성 절차 

## 5. Key Formats
사용자가 오류 없이 키를 쉽게 일고 쓸 수 있도록 하기 위해 사용  
### 5.1 개인키 formats
개인키는 256비트 -> 32바이트 -> 16진수 표현 64자리   
![그림4](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch4_4.png)  
그림4 개인키 포맷

```
$ ./bx wif-to-ec KzkpWYY7QZmV2v4U3LMHj3d1DBj3DTAchzy9jVpBfV5oh2bTY2KL
69841009fdbb8dac05e7dbe2d093fe1d8d36e050ac84db0c5a0641c36e45615f

$ ./bx base58check-decode KzkpWYY7QZmV2v4U3LMHj3d1DBj3DTAchzy9jVpBfV5oh2bTY2KL
wrapper
{
    checksum 1133447052
    payload 69841009fdbb8dac05e7dbe2d093fe1d8d36e050ac84db0c5a0641c36e45615f01
    version 128
}

$ ./bx base58check-encode 69841009fdbb8dac05e7dbe2d093fe1d8d36e050ac84db0c5a0641c36e45615f01 --version 128
KzkpWYY7QZmV2v4U3LMHj3d1DBj3DTAchzy9jVpBfV5oh2bTY2KL
```
### 5.2 공개키 formats

아래 예제는 python으로 작성된 key format code이다.(Running key-to-address-ecc-example.py)
```
$ python3 key-to-address-ecc-example.py
Private Key (hex) is:  6aab09ee2dfc3eb7e75638ca5ff4bf43e2728940feeb8fba5c3d8f916678cf70
Private Key (decimal) is:  48247361334930589481594495841992059859843736875128270815753879940829604794224
Private Key (WIF) is:  5JdGGvxhv3ifJSEvZTPdXKUnH9rzBzMTmiG4c64CRETKvmsha1z
Private Key Compressed (hex) is:  6aab09ee2dfc3eb7e75638ca5ff4bf43e2728940feeb8fba5c3d8f916678cf7001
Private Key (WIF-Compressed) is:  Kzo4RKAsb8zt3xQTQFUtCpZcrJ7dRW9KAM7UusK7PxPCXtZ9rDNK
Public Key (x,y) coordinates is: (95688209842730219301716110657101962663481178475860448940458861642428423340491, 38591482754941261609609094707882578042805919665486369567852338041417468276141)
Public Key (hex) is: 04d38d9b84f81fd70c54a868ef3163dc895cba53d53766513ae6c010ce01fc01cb55520153fcdda15b8586752f589f161c1157e1d19fe57a3997a3f8e5bdc0edad
Compressed Public Key (hex) is: 03d38d9b84f81fd70c54a868ef3163dc895cba53d53766513ae6c010ce01fc01cb
Bitcoin Address (b58check) is: 1EDAkoYYX2bkHJGbeFnQa962KoEoTYRWVy
Compressed Bitcoin Address (b58check) is: 1A5Ec43i2hSKb4Z1M9vMNWBQoefh8wsFLy
```

아래 코드 진행시 버전 차이로 인해 error 발생 코드 수정  
(참조 [ch4 addr error](https://hanscom95.github.io/all/bitcoin/%EB%A7%88%EC%8A%A4%ED%84%B0%EB%A7%81%20%EB%B9%84%ED%8A%B8%EC%BD%94%EC%9D%B8/issue/2022/02/24/bitcoinbook_ch4_addr_code_issue.html))
```
$ g++ -o addr ./addr.cpp -std=c++11 $(pkg-config --cflags --libs --static libbitcoin-system)
$ ./addr
Public key: 0202a406624211f2abbdc68da3df929f938c3399dd79fac1b51b0e4ad1d26a47aa
Address: 1PRTTaJesdNovgne6Ehcdu1fpEdX7913CK
```

## ○ 참고문서
* [bitcoinbook](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch04.asciidoc)
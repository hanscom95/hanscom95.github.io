---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch5"                    # (require) a string title
date: 2022-02-28 10:42:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch5 지갑

## 1. 서론 - 지갑
* 사용자 인터페이스 역할을 하는 주요 어플리케이션
* 사용자의 키를 저장하고 관리하는데 사용되는 데이터 구조
* 구조 파일이나 데이터베이스로 구현되는 개인키 저장소
* 개인키/공개키 쌍이 담겨있는 열쇠고리
* 지갑에는 키만 들어있으며, 코인은 들어있지 않다.

## 2. 지갑 기술의 개요
> 비트코인 지갑에는 키만 들어있으며, 코인은 들어있지 않다.  

* 지갑의 분류는 비결정적 지갑, 결정적 지갑으로 나뉜다.  

### 2.1 비결정적 지갑
* 각각의 키가 난수 생성기로부터 독립적으로 생성
* 지갑 내 키들은 서로 연관성 없음
* 무작위로 생성한 후 필요한 만큼 추가로 생성하며, 각 키들은 단 한번만 사용
  - 무작위로 뽑은 키들이 한번 생성된 후에는 모두 복사본을 보관해야 함
  - 지갑이 자주 백업되어야 함
* 주소를 다시 사용하게 되면 여러 건의 거래와 주소가 연관되기 때문에 프라이버시 노출  
* JBOK지갑(Just a Bunch Of Keys)  
![그림1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch5_1.png)  
그림1 비결정적 지갑: 무작위로 생성된 키 모음
  
### 2.2 결정적 지갑
* 단일 시드(Seed)로부터 많은 키를 쉽게 얻기 위해 개발
* 모든 키가 시드(Seed)라고 알려져있는 하나의 마스터 키에서 파생
* 지갑 내 모든 키들은 서로 연관 
* 시드(Seed) 원본을 가지고 있으면 누구나 다시 지갑 생성 가능

#### 2.2.1 일반 결정적 지갑
* 일반적으로 해시 함수를 이용하여 공통 시드에서 얻은 개인키 보관
* 시드는 개인키를 추출하기 위해 색인번호나 '체인코드'등의 여러 데이터와 무작위로 생성된 숫자가 결합되어 있는 형태  
![그림2](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch5_2.png)  
그림2 결정적(Seed) 지갑: 시드에서 파생된 결정론적 키 시퀀스
  
#### 2.2.2 HD(계층결정적) 지갑 [BIP-32/BIP-44]
* 결정적 지갑 중 가장 발달된 유형
* 트리 구조에서 생성된 키 보관
* BIP-32표준에서 규정
![그림3](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch5_3.png)  
그림3 결정적 지갑: 단일 시드에서 생성된 키 트리
  > 사용자들이 공개키에 대응하는 개인키에 접근하지 않고도 공개키 열을 생성할 수 있음. 안전하지 않은 서버 상에서나 수신 기능만을 가지고 있는 서버에서도 HD지갑 사용 가능하며 각각의 거래에 대해 다른 공개키를 발급 받을 수 있음

## 3. 자세한 지갑 기술
비트코인 지갑 기술이 발전함에 따라 상호운용성, 편리성, 안전성, 유연성 강화를 위한 공통의 산업 표준이 등장 
* BIP-39 기반 연상기호 코드 워드
* BIP-32 기반 HD 지갑
* BIP-43 기반 다용도 HD 지갑 구조
* BIP-44 기반 복수 계좌

### 3.1 BIP-39 기반 연상기호 코드 워드
* 결정적 지갑을 추출하기 위해 시드를 이용한 난수를 인코딩 하는 영어 단어열[니모닉(Mnemonic) 코드 워드]
* 단어열만 있으면 시드를 재현할 수 있고, 시드에서 지갑과 추출키 전부를 재현할 수 있다.
* 연상기호 코드는 무작위 순열에 비해 쉽게 읽히고 정확하게 입력되기 때문에 사용자들이 지갑을 백업하는 작업을 더욱 수월하게 해준다.
* 연상기호 코드를 이용해 결정론적 지갑을 실행하는 지갑 어플리케이션은 처음 지갑을 생성할 때 12-24 단어로 구성된 단어열을 사용자에게 보여준다. 단어열은 지갑을 백업해 놓은 것으로 동일한 지갑 어플리케이션이나 호환가능한 지갑 어플리케이션안에 들어있는 키 전부를 복원하고 재현하는데 사용할 수 있다.
![그림4](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch5_4.png)
그림4 엔트로피 생성해서 연상기호 워드로 인코딩 과정  
![그림5](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch5_5.png)
그림5 니모닉에서 시드로
    > 2048 라운드의 해싱이 포함된 키 홛장 기능은 니모닉 또는 암호 문구에 대한 무차별 대입 공격에 대해 효과적인 보호 기능(계산시 많은 비용 소모)  

### 3.2 시드로부터 HD 지갑 생성
* 128, 256, 512비트 크기의 무작위 숫자인 루트 시드(Root Seed) 한개로부터 생성된다. 이 시드는 연상기호에 생성되는 것이 가장 일반적
* 모든 키들은 이 루트 시드로부터 결정적으로 추출되며, 이 루트 시드 덕분에 호환 가능한 어떤 HD 지갑에서도 HD 지갑 전체를 재현할 수 있다. 수천 혹은 수백만 개의 키를 담고 있는 HD 지갑 이라도 루트 시다만 전송하면 백업이나 복원, export, import가 가능
* 루트 시드는 HMAC-SHA512 알고리즘에 입력되고, 그 결과로 나온 해시값이 마스터 개인키와 마스터 체인코드를 생성  
![그림6](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch5_5.png)
그림6 루트 시드에서 마스터 키와 체인코드 생성

## ○ 참고문서
* [bitcoinbook](http://https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch05.asciidoc)
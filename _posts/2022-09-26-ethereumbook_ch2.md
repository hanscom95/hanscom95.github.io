---
layout: post                           # (require) default post layout
title: "마스터링 이더리움 ch2"              # (require) a string title
date: 2022-09-26 19:00:00 +0900        # (require) a post date
categories: [all, ethereum, blockchain, 마스터링 이더리움]  # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 이더리움 ch2 이더리움 기초
'마스터링 이더리움 스마트 컨트랙트 및 댑 구축하기' 책의 세미나
제 2장 이더리움 기초


## 이더리움 기초
* 블록체인 기술을 기반으로 스마트 계약 기능을 구현하기 위한 분산 컴퓨팅 플랫폼
* 화폐명 이더(Ether)
* 단위 ETH 표기
* 시가 총액이 가장 높은 알트코인
* 가장 작은 단위 웨이(wei)   * 비트코인은 사토시
  * 1이더 = 100경 웨이(1*10^18)
* 지갑 : 이더리움 계정을 관리하는데 도움이 되는 애플리케이션
  * 메타마스크, 잭스, 마이이더월렛, 에메랄드 지갑
* 하나의 개인키가 하나의 계정
  * 키를 니모닉 단어 시퀀스로 백업
  * 디지털 정보로 저장 X


## 지갑 사용 방법
1. 크롬 웹 스토어에서 MetaMask 설치   
![ethereumbook_ch2_1.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_1.png)
2. 설치 진행   
![ethereumbook_ch2_2.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_2.png)
3. 비밀번호 만들기   
![ethereumbook_ch2_3.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_3.png)
4. 니모닉 진행1   
![ethereumbook_ch2_4.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_4.png)
5. 니모닉 진행2   
![ethereumbook_ch2_5.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_5.png)
6. 가입 완료 및 확장 프로그램 생성   
![ethereumbook_ch2_6.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_6.png)
7. 테스트 네트워크 사용   
![ethereumbook_ch2_7.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_7.png)
8. RopstenETH 테스트 입금 테스트   
![ethereumbook_ch2_8.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_8.png)
9. 트랜잭션 ID 탐색, Faucet 1이더 지급   
![ethereumbook_ch2_9.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_9.png)
10. 보낸 트랜잭션 생성   
![ethereumbook_ch2_10.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_10.png)
11. 가스비 활성화 및 선택   
![ethereumbook_ch2_11.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_11.png)

## 기본 스마트 컨트랙트 방법
### 월드 컴퓨터 소개
* 이더는 EVM에서 작업되는 스마트 컨트랙트를 실행하는데 사용
* EVM은 글로벌 싱글톤으로 전 세계에 걸친 단일 인스턴스 컴퓨터 처럼 작동

### 계정 유형
* 외부 소유 계정(Externally Owned Account, EOA)
  * 개인키가 있음
  * 자금 또는 스마트 컨트랙트 접근 제어
* 컨트랙트 계정(contract account)
  * EOA가 없는 스마트 컨트랙트 코드 있음
  * 개인키 없음
  * 스마트 컨트랙트 코드 로직 제어 가능
  * 솔리디티 컴파일러 작업
  * https://remix.ethereum.org 

### 리믹스 사용 테스트
1. 리믹스 접속 및 컴파일 
![ethereumbook_ch2_12.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_12.png)
2. 컴파일 코드
```json
{
  "linkReferences": {},
  "object": "608060405234801561001057600080fd5b5060f48061001f6000396000f3fe608060405260043610601f5760003560e01c80632e1a7d4d14602a576025565b36602557005b600080fd5b348015603557600080fd5b50605f60048036036020811015604a57600080fd5b81019080803590602001909291905050506061565b005b67016345785d8a0000811115607557600080fd5b3373ffffffffffffffffffffffffffffffffffffffff166108fc829081150290604051600060405180830381858888f1935050505015801560ba573d6000803e3d6000fd5b505056fea2646970667358221220617279e5e017c473508ed88e7488a6692e4afa3d7884ae53e11e751a20ab337864736f6c63430006040033",
  "opcodes": "PUSH1 0x80 PUSH1 0x40 MSTORE CALLVALUE DUP1 ISZERO PUSH2 0x10 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0xF4 DUP1 PUSH2 0x1F PUSH1 0x0 CODECOPY PUSH1 0x0 RETURN INVALID PUSH1 0x80 PUSH1 0x40 MSTORE PUSH1 0x4 CALLDATASIZE LT PUSH1 0x1F JUMPI PUSH1 0x0 CALLDATALOAD PUSH1 0xE0 SHR DUP1 PUSH4 0x2E1A7D4D EQ PUSH1 0x2A JUMPI PUSH1 0x25 JUMP JUMPDEST CALLDATASIZE PUSH1 0x25 JUMPI STOP JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST CALLVALUE DUP1 ISZERO PUSH1 0x35 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH1 0x5F PUSH1 0x4 DUP1 CALLDATASIZE SUB PUSH1 0x20 DUP2 LT ISZERO PUSH1 0x4A JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST DUP2 ADD SWAP1 DUP1 DUP1 CALLDATALOAD SWAP1 PUSH1 0x20 ADD SWAP1 SWAP3 SWAP2 SWAP1 POP POP POP PUSH1 0x61 JUMP JUMPDEST STOP JUMPDEST PUSH8 0x16345785D8A0000 DUP2 GT ISZERO PUSH1 0x75 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST CALLER PUSH20 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF AND PUSH2 0x8FC DUP3 SWAP1 DUP2 ISZERO MUL SWAP1 PUSH1 0x40 MLOAD PUSH1 0x0 PUSH1 0x40 MLOAD DUP1 DUP4 SUB DUP2 DUP6 DUP9 DUP9 CALL SWAP4 POP POP POP POP ISZERO DUP1 ISZERO PUSH1 0xBA JUMPI RETURNDATASIZE PUSH1 0x0 DUP1 RETURNDATACOPY RETURNDATASIZE PUSH1 0x0 REVERT JUMPDEST POP POP JUMP INVALID LOG2 PUSH5 0x6970667358 0x22 SLT KECCAK256 PUSH2 0x7279 0xE5 0xE0 OR 0xC4 PUSH20 0x508ED88E7488A6692E4AFA3D7884AE53E11E751A KECCAK256 0xAB CALLER PUSH25 0x64736F6C634300060400330000000000000000000000000000 ",
  "sourceMap": "162:412:0:-:0;;;;5:9:-1;2:2;;;27:1;24;17:12;2:2;162:412:0;;;;;;;"
}
```
3. 이더리움 블록체인 컨트랙트 등록1
![ethereumbook_ch2_13.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_13.png)
   
4. 이더리움 블록체인 컨트랙트 등록2
![ethereumbook_ch2_14.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_14.png)

5. 컨트랙트로 송금하기
![ethereumbook_ch2_15.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_15.png)
   
6. 송금 확인하기1
![ethereumbook_ch2_16.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_16.png)
   
7. 송금 확인하기2
![ethereumbook_ch2_17.png](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/ethereumbook_ch2_17.png)
---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch11"                    # (require) a string title
date: 2022-06-11 13:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch11 비트코인의 보안

## 1. 서론
  - 비트코인은 은행 계좌의 잔액처럼 가치에 대한 추상적 참조가 아니기 때문에 보하하는 것음 어려움


## 2. Security Principles
  - 비트코인의 핵심 원칙은 탈중앙화
  - 네트워크 보안은 액세스 제어가 아닌 작업 증명(PoW)을 기반으로 함
  - 당사자의 신원과 같은 개인정보를 공개 하지 않음  

## 3. 사용자 보안의 모범 사례
  - 디지털 자산은 여전히 단호한 적에게 매우 취약 
  - 비트코인 채택의 직접적인 결과로 우리는 하드웨어 암호화, 키 저장소 및 하드웨어 지갑, 다중 서명 기술, 디지털 에스크로 형태의 정보 보안 영역에서 띄어난 역량을 보임
  - 리스크 균형 맞추기
  - 리스크 다각화
  - 멀티시그와 거버너스
  - 생존력

## 4 물리적인 비트코인 저장 
* 물리적 형태
  - 종이에 인쇄된 비트코인 키 세트
  - BIP-38로 암호화
  - 비트코인을 오프라인으로 유지하는 것을 콜드 스토리지라 함
  - 인터넷에 연결되지 않은 시스템
* 하드웨어 지갑
  - 비트코인을 안전하게 보관하는 것 때문에 개발
  - 트레저(Trezor) 하드웨어 지갑

## 5 결론
  - 비트코인은 새로운 기술이 쉼 없이 발전 함에 따라 사용자 각자의 판단으로 보안을 신경 써야 함
  
## ○ 참고문서
* [bitcoinbook](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch11.asciidoc)
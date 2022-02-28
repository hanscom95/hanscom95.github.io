---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch3"                    # (require) a string title
date: 2022-02-16 15:00:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch3 비트코인 코어:참조구현(개발환경 세팅)
비트코인 코어 3장 개발환경 세팅  
풀 인덱스 노드 설정

**개발환경**  
docker 20.10.8 / Ubuntu 20.04 / BitcoinCore v22.0 / HDD 4tb / RAM 32GB

****
```
$ apt-get update
$ apt-get upgrade
$ apt-get install git
$ git clone https://github.com/bitcoin/bitcoin.git
$ git checkout v22.0
$ git status
$ apt-get install build-essential libtool autotools-dev automake pkg-config bsdmainutils python3
$ apt-get install libevent-dev libboost-dev
$ apt-get install libdb4.8-dev libdb4.8++-dev
$ apt-get install libqrencode-dev autoconf openssl libssl-dev libevent-dev
$ apt install libsqlite3-dev
$ apt install libminiupnpc-dev libnatpmp-dev
$ apt install systemtap-sdt-dev

$ ./autogen.sh
$ ./configure BDB_LIBS="/home/ubuntu/bitcoin/db4/lib -ldb_cxx-4.8" BDB_CFLAGS="/home/ubuntu/bitcoin/db4/include" --without-gui -prefix=/home/ubuntu/bitcoin/
$ ./contrib/install_db4.sh `pwd`
$ make
$ make install

$ bitcoind -datadir=/home/ubuntu/bitcoin/data -daemon
$ bitcoind -printconsole
$ chmod 766 -R ./data
```


**설치가 성공적으로 끝나면 API테스트**
```
$ bitcoin-cli getblockchaininfo
{
    "version": 150000, 
    "subversion": "/Satoshi:0.15.0/", 
    "protocolversion": 70015, 
    "localservices": "000000000000000d", 
    "localrelay": true, 
    "timeoffset": 0, 
    "networkactive": true, 
    "connections": 8, 
    "networks": [ 
    ... 
    detailed information about all networks (ipv4, ipv6 or onion) 
    ... 
    ], 
    "relayfee": 0.00001000, 
    "incrementalfee": 0.00001000, 
    "localaddresses": [ 
    ], 
    "warnings": "”
}

$ bitcoin-cli getblockhash 1000
00000000c937983704a73af28acdec37b049d214adbda81d7e2a3dd146f6ed09

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
    ... 
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
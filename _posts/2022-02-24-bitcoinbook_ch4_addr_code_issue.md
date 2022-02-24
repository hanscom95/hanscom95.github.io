---
layout: post                           # (require) default post layout
title: "마스터링 비트코인 ch4 addr code error"                    # (require) a string title
date: 2022-02-24 13:10:00 +0900       # (require) a post date
categories: [all, bitcoin, 마스터링 비트코인, issue]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# 마스터링 비트코인 ch4 addr code error. 'bitcoin/bitcoin.hpp' no such file or directory
마스터링 비트코인 ch4장에 나오는 cpp 예제 중 addr.cpp 아래 소스대로 되어야 할 부분이 버전 문제인지 에러가 발생 했다.  
찾아보니 libbitcoin 최신 버전에 맞게 컴파일 하는 방법이 밖인걸 확인 소스 수정과 실행 명령어를 바꿔 작업하니 잘돌아가네...

**예제**
```
# Compile the addr.cpp code
$ g++ -o addr addr.cpp -std=c++11 $(pkg-config --cflags --libs libbitcoin)
# Run the addr executable
$ ./addr
Public key: 0202a406624211f2abbdc68da3df929f938c3399dd79fac1b51b0e4ad1d26a47aa
Address: 1PRTTaJesdNovgne6Ehcdu1fpEdX7913CK
```

**에러**
![amazon-rds 1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/bitcoinbook_ch4_addr1.png.png)



**소스수정**
```
addr.cpp

#include <bitcoin/bitcoin.hpp> 
------------------------------  
#include <bitcoin/system.hpp>
```  
  
    
**실행명령어 추가(--static)**
```
$ g++ -o addr ./addr.cpp -std=c++11 $(pkg-config --cflags --libs --static libbitcoin-system)
$ ./addr
Public key: 0202a406624211f2abbdc68da3df929f938c3399dd79fac1b51b0e4ad1d26a47aa
Address: 1PRTTaJesdNovgne6Ehcdu1fpEdX7913CK
```


이래도 안될시 PKG_CONFIG_PATH가 안잡혀 있을 수 있다. home root에 ln 설정 해주면 해결 
```
$ vi  ~/.bahrc
export PKG_CONFIG_PATH=/path/to/libbitcoin-installation/lib/pkgconfig
```

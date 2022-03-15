---
layout: post                           # (require) default post layout
title: "evernym mobile sdk - simple sponsor"                    # (require) a string title
date: 2022-03-15 15:40:00 +0900       # (require) a post date
categories: [all, DID, ubuntu, docker, issue]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# evernym mobile sdk - Simple Sponsor install. 
evernym react-native-white-label-app을 설치중 Sponsor server 설치가 필요해서 정리해본다.


**개발환경**  
OS MAC M1 / docker 20.10.8 / Ubuntu:bionic /

**gitlab**
* [gitlab](https://gitlab.com/evernym/mobile/mobile-sdk/-/tree/main/examples/simple-sponsor)

위에 설치 진행 방식 중 docker로 설치했다. M1이라 ARM 아키텍쳐 구조로 docker platform 설정을 바꿔저야 error가 발생하지 않는다.  

위에 방식 그대로 할시 `Unable to install libindy error`가 발생한다.  

Dockerfile에서 시간대를 서울로 바꿔 주고 필요한 packge들 업데이트  

**Dockerfile**
```
FROM ubuntu:bionic
ENV DEBIAN_FRONTEND="noninteractive" TZ="Asia/Seoul"
ARG module
RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    gnupg \
    pbuilder \
    ubuntu-dev-tools \
    apt-file \
    software-properties-common \
    ca-certificates

RUN update-ca-certificates -f -v

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys CE7709D068DB5E88 &&\
    add-apt-repository "deb https://repo.sovrin.org/sdk/deb bionic stable"

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    python3 \
    python3-pip \
    libsodium-dev \
    libtool \
    pkg-config \
    build-essential \
    autoconf \
    automake \
    uuid-dev \
    wget \
    libindy

COPY . /app
WORKDIR /app
RUN sh zeromq-setup.sh

RUN pip3 install -r requirements.txt
EXPOSE 4321
CMD [ "python3", "server.py" ]
```

docker build `--platform amd64` 명령어 추가   

**docker build and run**
```
$ docker build --platform amd64 -f Dockerfile -t simple-sponsor .
$ docker run -it -p 4321:4321 -d simple-sponsor
```

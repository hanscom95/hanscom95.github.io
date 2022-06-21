---
layout: post                           # (require) default post layout
title: "Docker & Nexus3 Registry 설치 가이드"                    # (require) a string title
date: 2022-06-20 20:00:00 +0900       # (require) a post date
categories: [all, docker, ubuntu]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# Private Docker Registry 설치 가이드

개인 도커 repository가 필요해서 GUI 환경을 제공하는 오픈소스 기반 Nexus 설치 가이드 작성

## 데모 환경
* Ubuntu 20.10
* Docker version 20.10.8
  - 최소 1.8이상 필요
* Nexus 3.39.0-01
* MAC OS 12.4

## ubuntu 서버 작업
### 1. docker volume 생성
```
$ docker volume create nexus 
```
**주의 사항 - mount된 hdd 디렉토리를 container volume 호스트 디렉토리와 연결시 container down** 
### 2. docker contianer 생성(외부 포트 4000, 5000 번 연결)
4000번은 GUI환경 웹페이지, 5000번은 docker registry  
memory : 3g  
memory swap : 3g  
```
$ docker run -d -p 5000:5000 -p 4000:8081 --memory-swap 3g --memory 3g --name nexus -v nexus:/nexus-data -u root sonatype/nexus3
```
실행시간이 좀 걸림, 대략 1분?

### 3. nexus 웹 접속 http://서버ip:4000
admin 계정 로그인 패스워드는 container안에 /nexus-data/admin.password 확인
```
$ docker exec -it nexus cat /nexus-data/admin.password
1d671scx-3e78-1s2d-4455-2607d2aj186t
```

### 4. Nexus Blob Stores의 hosted, proxy 생성
Administration > Repository > Blob Stores > Create blob store  
  공통 type : File  
  1. name : docker-proxy  
  2. name : docker-hosted  
![create blob](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_1.png)


### 5. Nexus Repositories의 hosted, proxy 저장소 생성
Administration > Repository > Repositories > Create repository  
1. docker(hosted)  
   name : docker-hosted  
   http : 5000  
   Enable Docker V1 API : true  
   Storage > Blob store : docker-hosted 선택  
2. docker(proxy)  
   name : docker-hub  
   Enable Docker V1 API : true
   Proxy > Remote storage : https://registry-1.docker.io 입력  
   Docker index : Use Docker Hub  
   Storage > Blob store : docker-hub 선택  

![create repositories1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_2.png)

![create repositories2](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_3.png)

![create repositories3](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_4.png)

![create repositories4](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_5.png)

![create repositories5](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_6.png)

   
### 6. Nexus Realms 설정
Administration > Security > Realms  
**Docker Bearer Token Realm** Active로 이동 후 Save
![Nexus Realms](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_7.png)

### 7. docker 명령어 http 사용 설정(서버 ip 입력)

```
$ vi  /etc/docker/daemon.json
{
        "insecure-registries" : ["서버ip:5000"]
}

$ service docker restart
$ docker restart nexus
```

### 8. 외부에서 접속할 pc와 서버 docker 저장소 연결(mac os 기준)
```
$ vi /Users/[사용자이름]/.docker/daemon.json
{
        "insecure-registries" : ["서버ip:5000"]
}

$ service docker restart
$ docker login 서버ip:5000
Username: admin
Password: ***

WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

### 9. 서버 저장소에 push
```
$ docker tag myweb:1.0 서버ip:5000/myweb:1.0

$ docker push 서버ip:5000/mynode:1.0
The push refers to repository [서버ip:5000/myweb]
a5166b120798: Pushed 
8a725e53d751: Pushed 
b991c80c3ef2: Pushed 
8df6b63c60d4: Pushed 
d63b53686463: Pushed 
c0b09410617a: Pushed 
be9057e6dae4: Pushed 
4fc242d58285: Pushed 
1.0: digest: sha256:3ca3522ee4817d3656d7a1bcf4adce3fa8f926e8bb6c5f1ea7ccf6758cc5a88e size: 1984
```


### 10. 서버 저장소 push image 확인
Browse > Browse > docker-hosted > v2 > myweb > tags > 1.0
![create repositories1](https://raw.githubusercontent.com/hanscom95/hanscom95.github.io/master/static/img/_posts/docker-nexus-repository_8.png)


### 11. 외부에서 접속할 pc에서 서버 저장소에서 pull
```
$ docker pull 서버ip:5000/mynode:1.0
1.0: Pulling from myweb
df9b9388f04a: Pull complete 
5867cba5fcbd: Pull complete 
4b639e65cb3b: Pull complete 
061ed9e2b976: Pull complete 
bc19f3e8eeb1: Pull complete 
4071be97c256: Pull complete 
3faab898a72f: Pull complete 
0404bdb46d0c: Pull complete 
Digest: sha256:3ca3522ee4817d3656d7a1bcf4adce3fa8f926e8bb6c5f1ea7ccf6758cc5a88e
Status: Downloaded newer image for 서버ip:5000/myweb:1.0
서버ip:5000/myweb:1.0

$ docker images
REPOSITORY                  TAG       IMAGE ID       CREATED        SIZE
서버ip:5000/myweb            1.0       cc63874a7854   5 weeks ago    23.4MB
```


### 12. docker stop 명령어(databases to fully shut down)
```
$ docker stop --time=120 nexus
```

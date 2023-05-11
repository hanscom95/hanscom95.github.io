---
layout: post                                 # (require) default post layout
title: "AWS ALB ERR_TOO_MANY_REDIRECTS 에러"  # (require) a string title
date: 2023-05-08 19:00:00 +0900              # (require) a post date
categories: [all, devops, aws, ssl, error]   # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# AWS ERR_TOO_MANY_REDIRECTS 에러 발생
aws ALB를 통해 http -> https 리다이렉트 설정과 route53 연결 후 ERR_TOO_MANY_REDIRECTS 에러 발생   

## 해결방법
Apache 서버의 구성 파일이 잘못되 있을 가능성이 큼   
이번 문제는 ```.htaccess``` 파일의 리다이렉션 설정 파일과 충돌로 인해 발생   
```.htaccess``` 삭제 후 문제 해결   
**외부에서 redirection 해줬으면 내부에서는 안해주는 방향으로 설정**   

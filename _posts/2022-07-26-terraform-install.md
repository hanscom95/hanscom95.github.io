---
layout: post                          # (require) default post layout
title: "Terraform 설치 - MAC M1"       # (require) a string title
date: 2022-07-26 19:00:00 +0900       # (require) a post date
categories: [all, devops, terraform, aws]          # (custom) some categories, but makesure these categories already exists inside path of `category/`
---

# Terraform 설치 - MAC M1

MAC M1 로컬 pc Terraform 설치 가이드

## 데모 환경
* MAC OS 12.4

## terraform 설치 작업
### 1. install
```
$ brew install terraform
$ brew install kreuzwerker/taps/m1-terraform-provider-helper
$ m1-terraform-provider-helper activate
$ m1-terraform-provider-helper install hashicorp/template -v v2.2.0
```


### 2. init
```
$ terraform init
```

### 2. 빌드
```
$ terraform plan
```

### 3. 배포
```
$ terraform apply
```

### 4. 배포 삭제
```
$ terraform destroy
```
***주의 사항***  
> 오래 걸릴 수 있음

### 5. 생성된 리소스 확인
```
$ terraform state list
aws_launch_template.ecs_launch
...
...
```

### 6. 리소스 상세
```
$ terraform state show aws_launch_template.ecs_launch
```

### 7. 개발시 주의 사항

# **주의사항**
> <span style="color:red">로드밸런서 작업시 web AWS target group 생성 후 로드밸런서 **연동 하고** terraform 작업 진행 해야 됨!!!</span>

> target group은 AWS에서 수동으로 작업하는게 좋음

> ecs load_balancer 생성 시 health_check_grace_period_seconds < **300**   
> health check 404 error 발생 - 수치는 테스트 해봐야 됨
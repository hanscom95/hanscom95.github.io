---
layout: post                                    # (require) default post layout  
title: "Flutter Project clean"                  # (require) a string title  
date: 2022-04-25 21:00:00 +0900                 # (require) a post date  
categories: [all, flutter, issue, tip]          # (custom) some categories, but makesure these categories already exists inside path of `category/`  
---

# Flutter Project Clean 명령어 정리
flutter project를 clean 설치 하거나 git에서 브런치를 변경할때 가끔 build가 안될 경우와 이전 build와 충돌로 인해 에러가 발생할 경우 시도

**개발환경**  
OS MAC M1  
[✓] Flutter (Channel master, 2.6.0-12.0.pre.431, on macOS 11.6 20G165 darwin-arm, locale ko-KR)  
[✓] Android toolchain - develop for Android devices (Android SDK version 30.0.3)  
[✓] Xcode - develop for iOS and macOS (Xcode 12.4)  
[✓] Android Studio (version 4.1)  
[✓] VS Code (version 1.60.2)  


**명령어 정리**
```
$ flutter clean
$ ./ios/ rm -rf Pods/  
$ ./ios/ rm -rf .symlinks  
$ ./ios/ rm -rf Podfile.lock  

$ ./ios/ pod deintegrate  
$ flutter packages upgrade  
$ flutter pub upgrade --major-versions  
$ sudo gem install cocoapods  
$ ./ios/ pod repo update       
$ ./ios/ pod install --repo-update
$ flutter pub get  
``` 
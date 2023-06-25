---
title: "Great Developer Habits"
subtitle: "좋은 개발을 하기 위한 습관과 iOS 개발의 소소한 팁을 알아보자"
categories: iOS etc
date: 2023-02-25 04:40:39 +0000
last_modified_at: 2023-02-25 04:40:39 +0000
---

[WWDC19의 Great Developer Habits](https://developer.apple.com/videos/play/wwdc2019/239/) 를 보고 정리한 글입니다. 


> 🍏 Hidden details matter

## Organize

- group 을 사용해서 깔끔하고 잘 정돈된 프로젝트 사용하자
- 스토리보드도 섹션별로 나눠서 하자
- 미사용하는 코드는 주석처리하지 말고 지우자
- 워닝을 바로바로 처리하자. 새로운 워닝을 볼 수 없다.

## Track

- 작은 단위로 커밋하자
- 의미 있는 커밋명을 짓자
- 브랜치를 활용하자

## Document

- comment와 documentation을 활용하자

## Test

- unit Test 위주
- 코드 수행 전 단위테스트 실행 후 개발하자

## Analze

- 유용한 tool 소개: `NetworkLink` conditioner
    - 네트워크 상태에 따라서 다양한 테스트가 가능함
    - 다양한 조건에서 실험하여 race condition 등에 대한 테스트 등이 가능하다.
    
      ![스크린샷 2023-01-11 오후 11.55.43.png](Great%20Developer%20Habits%2058ee62ebf0774b5eaa632b0f285bc968/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-01-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_11.55.43.png)
    
- Use sanitizers and checkers:  Build Scheme에 따라서 사용할 수 있는 방법들
    - **Address Sanitizer**
        - 메모리 이슈나 버퍼 오버플로를 알려준다.
    - **Thread Sanitizer**
        - 데이터 경쟁상태에 있는지 확인할 수 있다.
    - **Undefined Behavior Sanitizer**
        - zero division 등 런타임에서 발생할 수 있는 undefined error를 해결할 수 있도록 하여 크래시를 미리 방지할 수 있다.
    - **Main Thread Checker**
        - AppKit, UIKit 등 메인스레드에서 실행되어야 하는 작업이 백그라운드에서 실행되고 있는지 확인한다.

## Evaluate

- 본인의 연습 차원에서 코드리뷰를 하자
- 각 코드를 이해하고
- 실제로 빌드해보자
- 테스트코드를 돌려보자

## Decouple

- pacakage, framework를 사용하면 중복코드를 줄이고 더욱 centralized 방식으로 관리할 수 있다.
- 만약 extension을 사용한다면 framework에 packaging한다면 메인앱과 extension 이 동일한 framework를 갖고 있기 때문에 앱의 바이너리 크기가 줄어들 것이다.

## Manange

dependancy에 대한 부분

- 만약 한 패키지가 관리가 안 되거나 없어진다면? 계획을 세워두기
- 디펜던시들을 제대로 이해하고 활용하기
- 사용자의 개인정보가 제대로 보호되는지 확인

이러한 개발을 습관화 한다면 시간은 적게 투자하지만 장기적으로 많은 시간을 절약할 수 있다.
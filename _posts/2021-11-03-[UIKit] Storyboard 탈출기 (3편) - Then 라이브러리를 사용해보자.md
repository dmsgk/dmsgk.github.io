---
title:  "[UIKit] Storyboard 탈출기 (3편) - Then 라이브러리를 사용해보자"
date:   2021-11-03 10:30:51 +0900
categories: iOS view-programming
---



이번 글에서는 Swift에서 종종 쓰이는 오픈소스 라이브러리 `Then` 에 대해 간단히 이야기해보려 합니다. 

이 시리즈에 `Then` 관련 내용을 넣을지 고민을 잠깐 했습니다. `Then` 라이브러리가 없으면 코드로 UI를 못 짜는 것도 아니기 때문에 꼭 알아야 하는 부분은 아닙니다. 그러나 코드로 UI를 짜면 관련된 속성을 계속 쓸 수밖에 없는데 `Then` 은 이걸 간편하게 쓰도록 도와주기 때문에 이번 시리즈에 포함시켰습니다. 

---

### Then 라이브러리가 무엇인고?

iOS 네임드 개발자이신 전수열님의 아주 유명한 오픈소스 라이브러리입니다.  => [Then 라이브러리 깃헙 바로가기](https://github.com/devxoul/Then)

문서가 잘 되어있어 읽으면 어떤 점이 유용한지 한 눈에 아실 것이라고 생각합니다. 그래서 간단하게 이야기하자면 `NSObject` 의 subclass에 속한 것들을 initialize할 때 클로저의 간소화된 `$0` 표시를 통해 중복을 줄이고 간편하게 쓸 수 있습니다. 



### Then 라이브러리를 다운로드 해보자

`Then` 라이브러리를 사용하기 위해서는 Then라이브러리를 받아와야 하는데 Cocoapod을 사용해 설치하거나 SPM을 활용해 설치하면 됩니다. 저는 Cocoapods을 통해 다운로드받았습니다.

#### Step0. (CocoaPods가 설치되지 않은 경우) CocoaPods 설치하기

터미널을 열어 다음과 같은 명령어를 입력해 CocoaPods를 설치합니다.

```
sudo gem install cocoapods
```

#### Step1. (만일 현재 프로젝트에 Podfile이 없다면) Podfile 생성하기

현재 프로젝트 위치에 가서 다음과 같은 명령어로 빈 Podfile을 생성합니다.

```bash
pod init
```

#### Step2. Podfile에 Then을 입력한다.

`vi podfile` 을 터미널에 입력하거나 아톰 등 편한 편집기로 podfile을 열어 다음과 같이 적어주고 저장합니다.

```
pod 'Then'
```

#### Step3. Pod 설치를 해준다.

터미널에 다음과 같은 명령어로 `Then` 을 설치해 줍니다.

```
pod install
```

그럼 Then 라이브러리를 해당 프로젝트에 사용할 준비가 끝났습니다. 이제 프로젝트를 `프로젝트명.workspace` 파일을 열어 원하는 라이브러리를 사용하면 됩니다!

### Then 라이브러리를 사용해보자

이제 본격적으로 Then을 사용하면 됩니다. 사용하고 싶은 파일에 다음과 같이 라이브러리를 불러와주시고

```swift
import Then
```

원하는 NSObject의 subclass를 초기화할 때 사용해주시면 됩니다. Then을 활용한 간단한 예시로 제가 원형버튼을 만들 때 쓰던 코드를 일부 수정해 가져와봤습니다. 

```swift
lazy var startStopBtn = UIButton().then {
        $0.layer.cornerRadius = 0.5 * $0.frame.size.width
        $0.setTitle("시작", for: .normal)
        $0.backgroundColor = .green
        $0.setTitleColor(.white, for: .normal)
        $0.addTarget(self, action: #selector(touchStartStopButton(_:)), for: .touchUpInside)
    }
```

사용해보니 간단하면서도 편하고 좋네요👍

---

간단하게 Then 라이브러리를 왜 사용하는지, 어떻게 사용할 수 있는지 알아보았습니다. Then 라이브러리가 편리해서 좋아서...귀감을 많이 받네요. 열심히  개발을 해야겠습니다. 그럼 다음 글로 찾아오겠습니다. 이만 총총..






---
title:  "[UIKit] Storyboard 탈출기 (4편) - AutoLayout을 Programmatically하게 적용하기"
date:   2021-11-19 10:30:51 +0900
categories: iOS view-programming
---



뷰를 구성하다 보니 frame-based layout의 한계를 느끼고 오토레이아웃의 필요성을 깨닫게 되어 이번 글에서는 코드로 Autolayout을 적용하는 기본적인 방법에 대해 알아보도록 하겠습니다.

> 참고사항: 이번 글은 Autolayout의 작동방식을 알고 스토리보드에서 Autolayout을 통해 UI를 구성할 수 있는 분을 예상 독자로 작성하였습니다.

### 1. 사용하는 방법

각 뷰를 화면의 하위 뷰로 넣어준 다음 해당 뷰에 대해 다음과 같이 `.translatesAutoresizingMaskIntoConstraints = false ` 처리해줍니다.

```swift
myView.translatesAutoresizingMaskIntoConstraints = false 
```

그리고 원하는 기준점을 바탕으로 오토레이아웃을 구현하면 됩니다.

```swift
myView.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true // x축 기준으로 화면 중앙에 오도록 함
```

예제를 한 번 살펴보겠습니다. 아래의 표와 같이 구현하고자 할 경우를 생각해보면,

| 버튼 하나와 텍스트라벨 하나가 있는 다음과 같은 화면을 오토레이아웃을 활용해 구성한다고 가정해보겠습니다. 이때 버튼은 가로세로 기준 정중앙에 배치되도록, 아래의 라벨은 x기준 중앙에 위치하도록 정렬하고 라벨의 top과 버튼의 bottom을 연결하는 오토레이아웃을 구현해보겠습니다. | [![오토레이아웃 예제](https://camo.githubusercontent.com/79274bf7ab1f25bde67c5d95e966151ae6d2070317b0789b7bb2f2f38dcd9d0a/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e6779316777356b353475656e386a33306e7130716f3734742e6a7067)](https://camo.githubusercontent.com/79274bf7ab1f25bde67c5d95e966151ae6d2070317b0789b7bb2f2f38dcd9d0a/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e6779316777356b353475656e386a33306e7130716f3734742e6a7067) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

우선 각각의 ui 프로퍼티를 만들어주고, 각 프로퍼티에 대해 오토레이아웃을 사용할 수 있도록 `myView.translatesAutoresizingMaskIntoConstraints = false `를 해줍니다.

```swift
private lazy var myBtn : UIButton = {
  let btn = UIButton()
  btn.setTitle("autoLayout적용예제", for: .normal)
  btn.backgroundColor = .blue
  btn.translatesAutoresizingMaskIntoConstraints = false
  return btn
}()

private lazy var titleLabel: UILabel = {
  let title = UILabel()
  title.text = "위의 버튼에 오토레이아웃 걸기"
  title.translatesAutoresizingMaskIntoConstraints = false
  return title
}()
```

그리고 요구사항을 충족할 수 있게끔 다음과 같이 오토레이이아웃을 걸어주면 됩니다.

```swift
override func viewDidLoad() {
  super.viewDidLoad()
  self.view.addSubview(titleLabel)
  self.view.addSubview(myBtn)

  myBtn.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
  myBtn.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
  myBtn.leadingAnchor.constraint(equalTo:
                                 view.safeAreaLayoutGuide.leadingAnchor, constant: 30.0).isActive = true

  titleLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
  titleLabel.topAnchor.constraint(equalTo: myBtn.bottomAnchor, constant: 20.0).isActive = true

}
```

이렇게 하면 원하는 대로 오토레이아웃이 걸리게 됩니다.

### 2. 코드 살펴보기

### 2.1. `translatesAutoresizingMaskIntoConstraints` 란 무엇인가?

`translates` + `AutoresizingMask` + `Into` + `Constraints` 이므로 AutoresizingMask를 Constraints로 바꾼다는 말같은데, 무엇인지 문서로 검색해보니 다음과 같이 설명하고 있습니다.

[![translatesAutoresizingMaskIntoConstraints](https://camo.githubusercontent.com/96682fa74e11c40b50a6f0ef07c623637bd363012e52055953a225fc082bba05/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e677931677731673066717533636a333138363063736d79732e6a7067)](https://camo.githubusercontent.com/96682fa74e11c40b50a6f0ef07c623637bd363012e52055953a225fc082bba05/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e677931677731673066717533636a333138363063736d79732e6a7067)

ㅇㅖ...? 더 자세히 보면 아래와 같이 적혀있습니다.

> ## Declaration
>
> ```swift
> var translatesAutoresizingMaskIntoConstraints: Bool { get set }
> ```
>
> ## Discussion
>
> **If this property’s value is true, the system creates a set of constraints that duplicate the behavior specified by the view’s autoresizing mask.** This also lets you modify the view’s size and location using the view’s `frame`, `bounds`, or `center` properties, **allowing you to create a static, frame-based layout within Auto Layout**.
>
> **Note that the autoresizing mask constraints fully specify the view’s size and position; therefore, you cannot add additional constraints to modify this size or position without introducing conflicts.** I**f you want to use Auto Layout to dynamically calculate the size and position of your view, you must set this property to false**, and then provide a non ambiguous, nonconflicting set of constraints for the view.
>
> **By default, the property is set to true for any view you programmatically create. If you add views in Interface Builder, the system automatically sets this property to false.**

만약 이 프로퍼티값이 참일 경우, frame기반인 autoresizing mask에 의해 만들어진 값을 오토레이아웃의 constraints로 바꾸어주는 것이라고 하네요. 하지만 그렇게 되면 오토레이아웃의 동적인 뷰의 크기나 위치 변화를 만들어낼 수 없게 된다는 점이 문제가 됩니다. 따라서 오토레이아웃에 따라 하위 뷰의 위치나 크기를 변경시키기 위해서는 해당 프로퍼티의 값을 false로 바꿔줘야 합니다.

코드로 뷰를 만들 경우 `translatesAutoresizingMaskIntoConstraints` 값은 기본값이 `true` 이고, 인터페이스 빌더로 뷰를 만들 경우 `translatesAutoresizingMaskIntoConstraints` 의 기본값이 `false` 라고 하네요. 따라서 코드로 뷰를 만들어 줄 때는 autolayout을 사용하려면 해당 인스턴스의 값을 `false` 로 만들어줘야 하나 봅니다.

### 2.2. `.isActive = true` 는 무엇인가?

[![isActive](https://camo.githubusercontent.com/cb1175a65dc3612b9175f5338f8103f26d32a9968fd64ea6314b6a4f3ae109f4/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e67793167776b68336b3666356f6a3330656630337730736e2e6a7067)](https://camo.githubusercontent.com/cb1175a65dc3612b9175f5338f8103f26d32a9968fd64ea6314b6a4f3ae109f4/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e67793167776b68336b3666356f6a3330656630337730736e2e6a7067)

`isActive` 는 이러한 오토레이아웃의 constraints를 활성화 하기 위한 프로퍼티네요.

### 3. 소소한 팁 : `NSLayoutConstraint.activate(consantsArray)`

모든 constraints를 활성화시키기 위해서는 각각 `.isActive = true` 를 붙여줘야 하는데요, 반복되는 것이 귀찮다 싶으면 각각의 NSLayoutConstraint를 배열에 담아 한꺼번에 전달한 후 `activate()` 메서드를 사용해 한꺼번에 activate할 수 있습니다.

[![스크린샷 2021-11-19 오후 4.11.49](https://camo.githubusercontent.com/9330524da6b5f86705839536baca8d546e7e74c009ae1057df5a1877544af269/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e67793167776b683969703769646a33306d693039723074342e6a7067)](https://camo.githubusercontent.com/9330524da6b5f86705839536baca8d546e7e74c009ae1057df5a1877544af269/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f3030386933736b4e67793167776b683969703769646a33306d693039723074342e6a7067)

위의 예제를 `NSLayoutConstraint.activate(consantsArray)` 를 사용해 바꾸면 다음과 같습니다.

```swift
  override func viewDidLoad() {
        super.viewDidLoad()
        self.view.addSubview(titleLabel)
        self.view.addSubview(myBtn)
        
        let consArr : [NSLayoutConstraint] = [
            myBtn.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            myBtn.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            myBtn.leadingAnchor.constraint(equalTo:
                                            view.safeAreaLayoutGuide.leadingAnchor, constant: 30.0),
            
            titleLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            titleLabel.topAnchor.constraint(equalTo: myBtn.bottomAnchor, constant: 20.0)
        ]
        
        NSLayoutConstraint.activate(consArr)
    }
```

중복되는 `isActivate = true` 를 없애고 배열로 간단하게 처리한 것을 볼 수 있습니다.

------

지금까지 programmatically하게 오토레이아웃을 쓰는 기본적인 방법을 알아보았습니다. 다음에는 `SnapKit` 을 활용한 방식도 소개해보도록 하겠습니다. 그럼 이만 총총..

### References

- https://baked-corn.tistory.com/36
- https://terry-some.tistory.com/122

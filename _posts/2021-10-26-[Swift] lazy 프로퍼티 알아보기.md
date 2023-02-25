---
title:  "[Swift] lazy 프로퍼티 알아보기"
date:   2021-10-26 11:11:51 +0900
categories: 
    - iOS 
    - Swift

---



이번 글에서는 Swift의 `lazy` 프로퍼티에 대해 알아보겠습니다. 

### lazy property란 무엇인가?

lazy property, 정확히 지연 저장 프로퍼티(lazy stored property)는 **처음 사용되기 전까지 초기값이 계산되지 않는 프로퍼티**를 의미합니다. 간단히 말해, 내가 만든 프로퍼티를 사용하기 전까지 메모리에 올려두지 않겠다는 의미입니다. 

lazy property는 프로퍼티의 초기값이 초기화가 끝나기 전까지 알 수 없는 외부 값에 의존적인 경우 유용합니다. 또한 초기값이 복잡한 계산나 셋업이 필요하지만 필요할 때까지 실행되지 않아야 할 때에도 유용합니다. 

> **주의할 점**
>
> `lazy` 키워드가 붙어있다고 해서 해당 프로퍼티가 한 번만 초기화된다는 것을 보장하는 것은 아닙니다. 만일 멀티스레드환경에서 해당 프로퍼티에 동시에 접근할 경우 여러 번 초기화될 가능성이 있습니다. 



### lazy property는 어떻게 사용하는가?

지연저장속성을 사용하려면 저장 프로퍼티의 앞에 `lazy` 를 붙여주면 됩니다. 여기서 하나 주의해야 할 점은 `lazy` 키워드는 `let` 과 어울릴 수 없고 **오로지 `var` 와만 어울린다는 것입니다.** Constant property인 `let` 은 초기화가 끝나기 전에 무조건 값을 갖고 있어야 하므로 `lazy` 와 어울릴 수 없습니다. 

간단한 예를 들어보겠습니다. 

하나의 뷰에 다양한 UI 프로퍼티를 넣고 싶은 경우를 가정해봅시다. 가장 쉬운 방법은 `viewDidLoad()` 에서 모든 프로퍼티를 넣어두는 것입니다. 그러나 다양한 프로퍼티가 들어가고 그 속성도 하나하나 코드로 지정할 경우 `viewDidLoad()` 가 지나치게 길어지고 복잡해지겠죠. 그럴 경우에도 `lazy` modifier를 사용할 수 있습니다. 

`UILabel` 을 예로 들어보면, 다음과 같이 `UILabel` 프로퍼티를 `ViewController ` 클래스 내에서 `lazy` 로 설정할 수 있습니다. 

```swift
lazy var myLabel: UILabel = {
    let myLabel = UILabel()
    myLabel.textAlignment = .center
    myLabel.textColor = .black
    myLabel.text = "Hello, World!"
    return myLabel
  }()
```

그리고 실제로 뷰가 메모리에 올라간 직후에 호출되는 함수인 `viewDidLoad()` 에서 `myLabel` 을 subView로 만들며 사용해주어 초기화작업을 해주게 됩니다. 

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view.
    self.view.addSubview(myLabel)
}
```

이렇게 `lazy` 를 이용하면 깔끔한 코드를 작성할 수 있네요.



---

간단히 `lazy` 키워드에 대해 공식문서를 참고해 작성해보았습니다. 그럼 이만 총총.. 

### Reference

- https://docs.swift.org/swift-book/LanguageGuide/Properties.html
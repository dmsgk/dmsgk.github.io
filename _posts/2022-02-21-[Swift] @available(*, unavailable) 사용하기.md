---
title:  "[Swift] @available(*, unavailable) 사용하기"
date:   2022-02-21 10:30:51 +0900
categories: 
    - iOS 
    - Swift
---

[10 little UIKit tips you should know](https://theswiftdev.com/10-little-uikit-tips-you-should-know/?utm_source=swiftlee&utm_medium=swiftlee_weekly&utm_campaign=issue_101) 라는 글을 읽다가 `@available(*, unavailable) ` 를 사용하는 코드를 여럿 보게 되었습니다. 왜 이걸 썼지? 했었는데 글 쓰신 분이 다음과 같이 남기셨습니다.

![@available](https://user-images.githubusercontent.com/72622744/213602729-e732d1ac-a4c4-4105-9ae5-5d2100880a82.jpeg)

개인적인 선호라.. 그렇담 쓰면 어떻게 좋다는지 좀 더 자세히 보고자 글을 쓰게 되었습니다. 



### Swift의 `@available ` attribute의 특징

Swift에서 `@` 표시를 사용해 1. declaration을 하거나 2. 타입에다가 특성을 부여해 추가적인 정보를 제공하는 attribute를 사용할 수 있습니다. 개발하면서 보게 되는 `@availble`, `@discardableResult`, `@escaping` 등이 이에 해당합니다. 

이 중에 `@available` 은 특정 플랫폼, Swift 버전, OS 버전 등의 제약을 걸 수 있습니다. 다음과 argument를 사용해 표시할 수 있습니다. 

> - `iOS`
> - `iOSApplicationExtension`
> - `macOS`
> - `macOSApplicationExtension`
> - `macCatalyst`
> - `macCatalystApplicationExtension`
> - `watchOS`
> - `watchOSApplicationExtension`
> - `tvOS`
> - `tvOSApplicationExtension`
> - `swift`

그리고 `unavailable`, `introduced` , `deprecated`, `obsoleted`, `message`, `renamed` 등의 argument를 사용해 추가적인 정보를 부여할 수 있습니다. 

`@available` 의 중요한 특징으로는 **compile-time**에 체크가 된다는 점입니다. 



### `@available(*, unavailable)` 을 사용하는 이유

[10 little UIKit tips you should know](https://theswiftdev.com/10-little-uikit-tips-you-should-know/?utm_source=swiftlee&utm_medium=swiftlee_weekly&utm_campaign=issue_101) 을 썼던 분이 `@available(*, unavailable)` 을 사용했던 부분을 살펴보면 다음과 같습니다. 

```swift
    // UIView 상속받은 클래스 내부

    @available(*, unavailable)
    override init(frame: CGRect) {
        super.init(frame: frame)
        
        self.initialize()
    }

    @available(*, unavailable)
    required public init?(coder: NSCoder) {
        super.init(coder: coder)
        
        self.initialize()
    }
```

코드로 뷰를 그리는데 오토레이아웃을 사용하고, xib을 사용하지 않도록 설정했습니다. 이런 경우 `required public init?(coder: NSCoder) `을  실행하면 다음과 같이 fatal Error가 발생하도록 만들어두는 경우가 일반적입니다. 

```swift
 required init?(coder: NSCoder) {
        fatalError("Not implemented required init?(coder: NSCoder)")
    }
```

그러나 이렇게 되면 코드 베이스로 뷰를 짜고 실수로 해당 init을 호출하게 되면 **runtime error**가 발생하게 됩니다. 반면에 해당 함수 선언하기 이전에 `@available(*, unavailable)` 을 적어두게 된다면 **compile time에서 error를 발생**시킵니다.따라서 실수를 컴파일 시에 미리 감지할 수 있다는 점에서 조금 편리하고 안전한 코드를 작성할 수 있다는 장점이 있겠네요. 

---

앱이 죽는 것은..마음 아픈 일인데 컴파일 타임에서 안전하게 관리할 수 있는 방법을 하나 배웠네요. 그럼 이만 총총..



### References

- https://theswiftdev.com/10-little-uikit-tips-you-should-know/?utm_source=swiftlee&utm_medium=swiftlee_weekly&utm_campaign=issue_101
- https://docs.swift.org/swift-book/ReferenceManual/Attributes.html
- https://ios-development.tistory.com/615
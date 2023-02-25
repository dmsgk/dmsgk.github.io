---
title:  "[Swift] final 키워드 이해하기"
date:   2021-08-15 11:11:51 +0900
categories: 
    - iOS 
    - Swift
---



최근에 모 코테를 봤는데..거의 기술면접같은 질문을 보고..😂 Swift라는 언어에 대해 차근차근 정리해보려고 합니다. 

이번에는 `final`  키워드에 대해 정리해보았습니다.

### `final` 의 개념

`final` 키워드는 상속을 막고자 할 때 사용합니다. 

상속을 막고자 하는 method, property 등의 앞에 `final` 을 붙여 사용하게 됩니다. (Ex) `final var`, `final func`, `final class func`, and `final subscript`

또한 class 전체에 `final` 키워드를 붙여 클래스 전체에 대한 상속을 막을 수도 있습니다. (`final class`)

만일 final 키워드가 붙어있는 것을 상속하여 하면 compile-time-error가 나게 됩니다. 

### `final` 과 성능

`final`, `private` 그리고 모든 module optimization은 Dynamic Dispatch를 줄여주어 성능을 향상시킵니다. 

> **Dynamic Dispatch 란? **
>
> Swift는 클래스가 메소드나 프로퍼티 등을 상속할 수 있습니다. 이는 프로그램이 런타임에 어떤 메소드나 프로퍼티가 관련이 있는지 결정하고, 이러한 indirect call, indirect access를 실행해야 한다는 것을 의미합니다. 이를 dynamic dispatch라고 합니다. 

Dynamic Dispatch는 상속을 사용할 때 필요한 방식이지만, Swift에서는 클래스마다 vtable을 갖고 이를 참고하며 함수가 호출되므로 그에 따른 오버헤드가 발생하게 됩니다.

 `final`로 선언된 요소는 상속이 일어나지 않으므로 dynamic dispatch를 사용하지 않고 직접적인 접근을 할 수 있습니다.  즉 컴파일러는 indirect call, indirect access 를 무시할 수 있게 되므로 성능의 향상을 기대할 수 있습니다. 



### References

- https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html#ID202
- https://developer.apple.com/swift/blog/?id=27
- https://onemoonstudio.tistory.com/7
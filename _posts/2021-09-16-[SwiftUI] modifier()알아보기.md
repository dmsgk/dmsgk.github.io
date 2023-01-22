---
title:  "[SwiftUI] modifier() 알아보기"
date:   2021-09-16 10:30:51 +0900
categories: ios view-programming

---



![Challenge: Prototype with SwiftUI - Discover - Apple Developer](https://devimages-cdn.apple.com/wwdc-services/articles/images/00E4BA9F-2967-41D8-9F3A-29DF9381F05C/2048.jpeg)

## modifier(_:) 란?

예시를 통해 modifier() 메소드가 무엇인지 알아봅시다. 

아래 코드에서  텍스트뷰 `Text("Hello, world!")`에 붙어있는 `opacity()`, `boarder()` 이 `modifer()` method에 해당합니다.

```swift
struct ContentView: View {
    var body: some View {
      Text("Hello, world!")
      .opacity(0.5)
      .border(Color.red, width: 2)
    }
}
```

그렇다면 이렇게 뷰에 붙어있는 modifer() 메소드는 어떤 역할을 하느냐? 하면 공식문서에는 이렇게 쓰여있습니다.

> Applies a modifier to a view and returns a new view.

즉, modifier() 메소드는 뷰 모디파이어가 적용된 새로운 뷰를 만들어주고, **그 새로운 뷰를 리턴**시킵니다.

예를 들어 뷰의 투명한 정도를 조정하는 View Modifier인 `opacity` 의 함수를 보면 투명도가 적용된 새로운 View를 리턴하는 것을 확인할 수 있습니다. 

![def](https://user-images.githubusercontent.com/72622744/213933625-9fa54008-8c50-4f6d-a1e7-1bdb746b14a2.jpeg)


## modifier(_:)의 순서는 중요하다

각 뷰에 대한 modifier()의 순서가 왜 중요하냐! 하면 앞에서 살펴본 것처럼 modifer는 각 메소드를 수행하면서 새로운 뷰를 리턴하기 때문입니다.

무슨말인지 예를 들어보겠습니다. Text View를 같은 종류의 modifier 들이 꾸미고 있지만 순서가 다른 경우를 보겠습니다.

첫 번째 경우, 투명도를 담당하는 `opacity()`가 경계값을 그려주는 `boarder()`보다 먼저 왔습니다.

```swift
struct ContentView: View {
    var body: some View {
      Text("Hello, world!")
        .font(.largeTitle)
        .opacity(0.5)
        .border(Color.red, width: 2)
      
    }
}
```

두 번째 경우, `boarder()`가 `opacity()`보다 먼저 왔습니다.

```swift
struct ContentView: View {
    var body: some View {
      Text("Hello, world!")
        .font(.largeTitle)
      .border(Color.red, width: 2)
      .opacity(0.5)
    }
}
```



그 결과는 다음과 같습니다!

| 첫 번째 경우(opacity -> boarder)                             | 두 번째 경우(boarder -> opacity)                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![first](https://user-images.githubusercontent.com/72622744/213933562-f6c1104e-44da-4662-95a7-a9981abaa665.jpeg) | ![second](https://user-images.githubusercontent.com/72622744/213933565-b51177ad-da1c-4573-98f8-b8a723b96b6d.jpeg) |





첫 번째 경우는 투명도가 텍스트에 적용된 후, boarder를 적용하였기 때문에 `boarder` 는 기본값 그대로인 반면, 두 번째 경우는 `boarder` 를 먼저 적용하고 리턴된 뷰에 투명도를 적용하려 리턴하였기 때문에 `boarder` 까지 반투명해진 모습을 확인할 수 있습니다. 

이처럼 순서에 따라 어떤 뷰에다 modifier()메소드를 적용할지가 결정되기 때문에 순서를 잘 고려해야 원하는 뷰를 효과적으로 만들 수 있겠습니다.



## modifier(_:)의 종류

Cmd + shift + L 을 눌러 2번째 탭에 있는 modifier library에서 자세한 modifier()의 종류를 확인할 수 있습니다. 

![modifier](https://user-images.githubusercontent.com/72622744/213933630-c34f22a5-3c58-42f1-8d2e-de0106a0c3f5.jpeg)



---

아주 간단하게 modifier()에 대해 알아보았습니다. 

다음번에는 `ViewModifer` 프로토콜에 대해서도 글을 써보도록 하겠습니다. 이만 총총..

## References

- https://developer.apple.com/documentation/swiftui/view/modifier(_:)






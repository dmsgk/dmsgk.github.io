---
title:  "[Swift][SwiftUI] PropertyWrapper의 개념, @State, @Binding"
date:   2022-11-12 10:30:51 +0900
categories: iOS view-programming


---



## propertyWrapper의 개념

- Swift 5.1 부터 사용할 수 있는 기능
- 한번 원하는 기능 또는 연산을 하여(wrapped) 하여 보여줄 수 있도록 한다.
- Class, Struct, Enum 에서 `@propertyWrapper` 를 붙여 사용한다. 
- propertyWrapper 를 정의하기 위해서는 `wrappedValue` 라는 프로퍼티를 정의해야 한다. 

---



- Swift Docs의 예제: 12 이하의 값을 얻어오는 연산을 줄이기 위해 만들 수 있는 `TwelveOrLess` 라는 propertyWrapper 만드는 예시

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}
```

- 사용할 때는 다음과 같이 사용한다. 

```swift
struct SmallRectangle {
    @TwelveOrLess var height: Int
    @TwelveOrLess var width: Int
}

var rectangle = SmallRectangle()
print(rectangle.height)
// Prints "0"

rectangle.height = 10
print(rectangle.height)
// Prints "10"

rectangle.height = 24
print(rectangle.height)
// Prints "12"
```



### References

- https://docs.swift.org/swift-book/LanguageGuide/Properties.html



## SwiftUI에서 자주 사용하는 두 가지 PropertyWrapper: @State, @Binding

- `@State`
  - SwiftUI에서는 해당 값으로 뷰를 업데이트할 수 있음
  - 직접 접근(read)하여 값을 변경(write) 할 수 있음
  - 만일 자식뷰(child View) 가 있다면 부모뷰에서 `@State` 가 붙은 값에 변화가 생기면 자식뷰를 업데이트시킨다. 그러나 자식뷰가 직접 저장프로퍼티 값을 변화시킬 수는 없다. 

- `@Binding`
  - 만일 자식뷰가 부모의 저장프로퍼티 값을 변화시키고 싶다면 사용할 수 있다. 
  - 예를 들어 즐겨찾기 버튼에 딸린 isSet 의 변수가 있다고 가정할 때, 뷰 안에 버튼을 넣어둔 상태에서 버튼을 누른 값이 뷰 안의 저장프로퍼티를 변화시키고 싶을 때 사용할 수 있다. 
  - 2-way-binding을 하게 해 준다. `@Binding` 을 사용하면 방금 든 예시의 경우 뷰는 버튼을 초기화할 때 1. **뷰가 갖고 있는 state 값을 바인딩하여 넘겨주고**  2. **버튼의 `@Binding` 으로 쓰인 값은 변경될 때마다 뷰를 업데이트**하게 된다. 

```swift
import SwiftUI

struct PlayButton: View {
    @Binding var isPlaying: Bool

    var body: some View {
        Button(isPlaying ? "Pause" : "Play") {
            isPlaying.toggle()
        }
    }
}

struct PlayerView: View {
    var episode: Episode
    @State private var isPlaying: Bool = false

    var body: some View {
        VStack {
            Text(episode.title)
                .foregroundStyle(isPlaying ? .primary : .secondary)
            PlayButton(isPlaying: $isPlaying) // Pass a binding.
        }
    }
}
```


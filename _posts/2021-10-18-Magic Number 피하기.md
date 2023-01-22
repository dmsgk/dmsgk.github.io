---
title:  "Magic Number 피하기"
date:   2021-10-18 11:11:51 +0900
---



이번 시간에는 Magic Number가 무엇인지 알아보고 매직넘버의 문제점은 무엇인지, 어떻게 하면 magic number를 적게 사용할 수 있는지 알아보겠습니다. 

### Magic Number란 무엇인가?

[위키피디아](https://en.wikipedia.org/wiki/Magic_number_(programming))에 따르면 프로그래밍에서의 magic number는 여러 의미가 있다고 합니다.  그러나 이번 글에서 이야기고자 하는 **magic number는 하드코딩된 상수값**을 의미합니다. 

예를 들어, 다음과 같은 SwiftUI 파일이 있다고 하면,

```swift
struct ContentView: View {
    var body: some View {
        Text("hello")
            .padding()
            .foregroundColor(.white)
            .background(
                Color(.gray)
            )
            .cornerRadius(12.0)
    }
}
```

곡률값을 표시하기 위해 `cornerRadius` modifier에 쓰인 `12.0` 값 부분을 magic number라고 할 수 있습니다. 



### Magic Number의 문제점

Magic Number는 안티패턴으로도 불리는데요, 그렇다면 magic number는 무엇이 문제이길래 피해야 하는 걸까요? 두 가지 문제점을 생각해볼 수 있습니다.

첫째, Magic number는 코드의 가독성을 떨어뜨릴 우려가 있습니다. 협업을 하며 개발을 할 때 숫자가 하드코딩되어 있으면 코드를 작성하지 않은 사람은 이것이 무엇을 의미하는지 이해하기 어렵고 어떤 규칙이 적용되고 있는지 이해하기 어렵습니다. 

둘째, Magic number를 사용할 경우  유지보수가 번거로워집니다. 만일 버튼의 곡률을 일률적으로 12.0으로 설정해두었다 디자이너의 요청에 맞춰 21.0으로 변경하고자 할 경우, 각각의 버튼마다 하드코딩된 매직넘버가 있다면 하나하나를 전부 고쳐야 해서 상당히 번거로울 것입니다. 



### Magic Number를 줄이려면 어떻게 해야 할까?

 이와 같은 문제를 해결하기 위해 `enum` 타입에 필요한 상수를 넣어두고 필요할 때마다 사용할 수 있습니다. 

만일 앞서 예시로 봤던 magic number를 없애기 위해 다음과 같이 파일을 하나 만들어둔다면, 

```swift
import Foundation
import UIKit

enum Constants {
    enum General {
        public static let buttonCornerRadius = CGFloat(12.0)   
    }
}
```

SwiftUI 파일을 다음과 같이 바꿀 수 있습나다.

```swift
struct ContentView: View {
    var body: some View {
        Text("hello")
            .padding()
            .foregroundColor(.white)
            .background(
                Color(.gray)
            )
            .cornerRadius(Constants.General.buttonCornerRadius)
    }
}
```



이렇게 nameSpace를 활용하면 가독성도 높아지고 수정 시에도 `enum` 부분을 찾아가서 수정하면 되므로 유지보수도 수월할 것입니다. 

하지만 그렇게 하기 위해서는 네이밍을 잘 해야 코드를 읽기 쉬울 테니 좋은 이름을 지어주기 위해 고심은 필수일 것입니다..

---

간단하게 magic number에 대해 알아보았습니다. 지금까지 제 코드는 magic number 투성이었는데 공부하면서 반성하게 되었습니다. 앞으로도 클린한 코드를 위한 공부를 계속해봐야겠습니다. 이만 총총..



### References

- https://en.wikipedia.org/wiki/Magic_number_(programming)
- https://stackoverflow.com/questions/47882/what-is-a-magic-number-and-why-is-it-bad


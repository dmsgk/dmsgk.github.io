---
title:  "[UIKit] Storyboard 탈출기 (2편) - 스토리보드 대신 SwiftUI의 프리뷰 사용하기"
date:   2021-10-28 10:30:51 +0900
categories: ios view-programming
---



스토리보드를 없애면서 관리해야 할 부분이 코드로 집중돼 편하기도 하지만 뷰를 제대로 만들었는지 확인하려면 매번 시뮬레이터를 돌려봐야 한다는 점은 불편했습니다. 그래서 이번 글에서는 스토리보드 대신 SwiftUI의 프리뷰를 사용하여 매번 시뮬레이터를 돌리지 않고도 뷰를 미리보기하면서 개발하는 방법을 알아보도록 하겠습니다. 

>**Deployment target**
>SwiftUI를 사용해야 하므로 Deployment target이 iOS 13.0 이상은 되어야 합니다.

(작성 시 Xcode 버전: 12.5.1)

---

### 1. 사용법

결론부터 간단히 말하자면 `ViewController()` 라는 뷰컨트롤러의 미리보기를 추가하고 싶다면 다음과 같은 코드를 해당 파일이 제일 밑에 추가해주면 됩니다. 

```swift
#if DEBUG
import SwiftUI

struct MyVCRepresentable : UIViewControllerRepresentable {
    @available(iOS 13.0, *)
    
    // ui VC 만들기
    func makeUIViewController(context: Context) -> some UIViewController {
        print(#fileID, #function, #line, "")
        return ViewController()
    }
    
    func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) {
        print(#fileID, #function, #line, "")
    }
    
}

@available(iOS 13.0, *)
struct MyVCRepresentable_Previews: PreviewProvider {
    static var previews: some View {
        MyVCRepresentable()
    }
}

#endif

```

그러면 아래처럼 아름답게 SwiftUI로 뷰를 구성하는 것처럼 프리뷰가 뜨게 됩니다 😲😆 수없이 빌드하던 시절은 이제 안녕이다...라는 감동이..드네요.

![preview](https://user-images.githubusercontent.com/72622744/213934498-ecd5eb77-34d1-4263-9b75-ced2d3104583.jpeg)

---

### 2. 왜 이렇게 쓸 수 있을까?

그렇다면 왜 이렇게 쓰면 SwiftUI 프리뷰를 볼 수 있느냐? 하면 아시는 분도 있겠지만 `UIViewControllerRepresentable` 프로토콜 덕분입니다. 앞서 만든 struct `MyVCRepresentable` 을 보면 해당 프로토콜을 따르고 있다고 해놓았죠? 공식문서를 보면`UIViewControllerRepresentable` 을 다음과 같은 프로토콜이라고 소개하고 있습니다.

![uiviewcontrollerRepresentable](https://user-images.githubusercontent.com/72622744/213934502-17f70176-d535-4e03-be9b-ca57baa32383.jpeg)

즉 UIKit의 VC를 나타내는 SwiftUI의 뷰를 나타내는 프로토콜입니다. 이 프로토콜을 준수하는 경우 두 가지 함수를 사용해야 하는데 앞서 사용했던 `makeUIViewController()` 와 `updateUIViewController()` 가 그것입니다. 

![representable2](https://user-images.githubusercontent.com/72622744/213934508-78a3e1be-fa2d-4907-9949-5188bca0ad2f.jpeg)

이 두 가지 함수를 사용해 UIKit에서 원하는 뷰컨트롤러를 받아와 업데이트를 하도록 합니다. 그리고 이것을 프리뷰로 보여줌으로써 SwiftUI의 프리뷰를 사용할 수 있게 되는 것입니다.

---

공부하다 보니 `UIViewControllerRepresentable` 프로토콜은 스토리보드 대신 보여주는 상황 뿐 아니라 SwiftUI와 UIKit을 서로 함께 쓰기 위해 다방면으로 활용될 수 있다는 생각이 듭니다. 

 `UIViewControllerRepresentable` 에 대해 더 자세히 보고 싶으신 분은 애플의 SwiftUI 튜토리얼에서 해당 부분을 참고하면 도움이 될 것 같습니다.  => https://developer.apple.com/tutorials/swiftui/interfacing-with-uikit

그럼 이만 총총..

### References

- https://developer.apple.com/tutorials/swiftui/interfacing-with-uikit

- https://www.youtube.com/watch?v=0TZTjxsuqd0&list=PLgOlaPUIbynoQIcChkQXuGEPXf0Rl8ImH&index=18

  
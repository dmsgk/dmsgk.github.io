---
title:  "[SwiftUI] Zstack 알아보기"
date:   2021-09-28 10:30:51 +0900
categories: iOS view-programming
---



Hstack, Vstack은 UIKit을 활용할 때에도 Horizontal stack, Vertical stack을 사용했던 기억에 비슷하다고 느꼈는데 Zstack은 처음 만났습니다. 그래서 이번 시간에는 Zstack이 무엇인지 알아보려고 합니다. 



### Zstack이란?

Zstack의 z는 x, y, z축을 말할 때의 z입니다. 우리가 3차원을 배울 때 z축이 등장하죠. 우리가 보는 화면은 가로, 세로 2차원인데 핸드폰 화면에 놓인 뷰 위에 다른 뷰를 쌓아가는 것을 z축이라고 생각하면 됩니다.  

그렇게 생각하면 Zstack은 자식 뷰(child view)들이 화면에 스택처럼 쌓이는 구조를 의미합니다. 그말은 뒤에 나오는 자식 뷰가 그 전의 자식 뷰 위에 쌓이는 structure를 의미합니다. 아래 apple 문서의 예제 코드를 보면서 이해해 봅시다. 

```swift
let colors: [Color] =
    [.red, .orange, .yellow, .green, .blue, .purple]

var body: some View {
    ZStack {
        ForEach(0..<colors.count) {
            Rectangle()
                .fill(colors[$0])
                .frame(width: 100, height: 100)
                .offset(x: CGFloat($0) * 10.0,
                        y: CGFloat($0) * 10.0)
        }
    }
}
```

`color` 프로퍼티를 활용해 6색의 동일한 크기의 정사각형을 만들어 `Zstack` 에 넣었습니다. 그렇게 되면 아래와 같이 빨강, 주황, 노랑, 초록, 파랑, 보라 순으로 화면 위에 쌓이게 되는 것을 확인할 수 있습니다. 

  ![zstack2](https://user-images.githubusercontent.com/72622744/213932041-4bda6f81-26a1-4c67-ac6e-e2c85d2c79e3.jpeg)





만일 아래의 코드처럼 `offset()` modifier method를 생략한다면 이전과 동일하게 빨강, 주황, 노랑, 초록, 파랑, 보라 순으로 뷰가 쌓이지만 모두 겹쳐진 상태라 다음과 같이 보라색만 보이게 되는 것을 알 수 있습니다. 

```swift
let colors: [Color] =
    [.red, .orange, .yellow, .green, .blue, .purple]

var body: some View {
    ZStack {
        ForEach(0..<colors.count) {
            Rectangle()
                .fill(colors[$0])
                .frame(width: 100, height: 100)
               // .offset(x: CGFloat($0) * 10.0,
               //         y: CGFloat($0) * 10.0)
        }
    }
}
```

![zstack](https://user-images.githubusercontent.com/72622744/213931978-c91a2ff3-5ffa-4d51-9670-901e7f095340.jpeg)

Zstack은 `Alignment` (정렬) 속성을 사용해 Zstack에 속한 자식 뷰들을 쌓아나갈 때 x축과 y축의 정렬 기준을 결정할 수 있습니다. 기본값은 `center` 입니다. `Alignment` 의 종류는 다음과 같습니다. 

![A table showing the various alignment guides next to each](https://docs-assets.developer.apple.com/published/51307708283804cafcdfd0395887cc7a/10700/SwiftUI-Alignment-table@2x.png)



apple문서의 아래의 예제를 하나 보겠습니다.

```swift
var body: some View {
    ZStack(alignment: .bottomLeading) {
        Rectangle()
            .fill(Color.red)
            .frame(width: 100, height: 50)
        Rectangle()
            .fill(Color.blue)
            .frame(width:50, height: 100)
    }
    .border(Color.green, width: 1)
}
```



Zstack에  `bottomLeading` alignment를 사용했으므로, `bottom` 그리고 `leading` 을 기준으로 빨간색 직사각형과 파란 직사각형이 쌓이게 됩니다.

내친 김에 아홉가지 경우의 `Alignment` 의 경우를 바꿔가며 정리해보았습니다. 그림으로 보면 이해가 쉬울 것이라고 생각합니다.

| topLeading                                                   | top                                                          | topTrailing                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![topleading](https://user-images.githubusercontent.com/72622744/213931737-c390f9f1-bd81-48fd-99e6-edbbeea31cb0.jpeg) | ![top](https://user-images.githubusercontent.com/72622744/213931823-468e0f38-fb3c-455d-a419-1686d27a989f.jpeg) | ![topTrailing](https://user-images.githubusercontent.com/72622744/213931827-ffd2c629-9466-42fd-8731-a1246df00bf0.jpeg) |
| **leading**                                                  | **center**                                                   | **trailing**                                                 |
| ![leading](https://user-images.githubusercontent.com/72622744/213931835-0c08a8b0-fe83-4b24-a0e6-17bf29f477ca.jpeg) | ![center](https://user-images.githubusercontent.com/72622744/213931868-c21dbb4e-d479-483b-9cc0-73e8a3402a25.jpeg) | ![trailing](https://user-images.githubusercontent.com/72622744/213931872-7e19c622-57ff-430f-8eac-35b09498caba.jpeg) |
| **bottomLeading**                                            | **bottom**                                                   | **bottomTrailing**                                           |
| ![bottomLeadling](https://user-images.githubusercontent.com/72622744/213931879-9c7c2f8e-20f5-4328-ad07-eba8b53417cd.jpeg) | ![bottom](https://user-images.githubusercontent.com/72622744/213931881-ada9b121-621e-426c-a936-74f0c8bbdeb9.jpeg) | ![bottomTrailing](https://user-images.githubusercontent.com/72622744/213931887-cb9f5223-a904-4e9c-ba66-52b438d0c381.jpeg) |



---

이번 글에서는 Zstack에 대해 알아보았습니다. 뷰의 배경을 설정하거나 뷰들을 쌓아올릴 일이 있을 때 Zstack을 활용해보아야겠습니다. 그럼 이만 총총..



### References

- https://developer.apple.com/documentation/swiftui/zstack 
- https://developer.apple.com/documentation/swiftui/alignment
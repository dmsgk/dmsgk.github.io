---
title:  "[UIView] isExclusiveTouch로 중복터치를 막아보자"
date:   2022-08-08 10:30:51 +0900
categories: iOS view-programming
---



최근에 일을 하면서 화면에 버튼이 여러 개 있을 때 여러 버튼을 동시 클릭하면 각 버튼이 연결하는 화면들이 중복으로 열리는, 중복 클릭으로 인한 이슈가 나왔습니다. 해당 이슈를 `isExclusiveTouch` 라는 속성을 사용하여 해결하셨길래 공부해보고자 글을 남깁니다. 

### isExclusiveTouch 

공식 문서에는 다음처럼 적혀 있습니다.

```
A Boolean value that indicates whether the receiver handles touch events exclusively.
```

터치 이벤트를 독점적으로 받을지 여부를 조정할 수 있는 속성입니다. 해당 속성은 `UIView`의 인스턴스 프로퍼티로 저장되어 있으며 해당 프로퍼티의 값을 변경하지 않을 경우 기본값으로 `false` 로 들어갑니다.  



### isExclusiveTouch 는 중복터치를 어떻게 막는가?

중복으로 선택되지 않아야 하는 UIView 들의 isExclusiveTouch 의 값을 true로 설정합니다. 그렇게 되면 하나의 뷰에서 터치 이벤트를 받게 되면 동일한 window에 있는 다른 뷰의 터치이벤트의 전달을 막게 됩니다. 이에 따라서 여러 뷰의 버튼을 동시에 누르더라도 여러 개의 뷰에서 터치 이벤트를 동시에 처리하지 않도록 하면서 중복터치가 불가능하게 됩니다. 



### References

- https://developer.apple.com/documentation/uikit/uiview/1622453-isexclusivetouch/
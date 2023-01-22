---
title:  "[Swift] DispatchQueue의 qos 사용하기"
date:   2021-11-15 10:30:51 +0900
categories: ios swift
---

GCD에서 global큐나 커스텀 큐를 사용할 때 `qos(quality of service)`를 사용해 작업의 중요도를 결정할 수 있습니다. 그렇기 때문에 작업의 우선순위를 결정하기 위해서는 qos의 우선순위가 어떻게 되는지 알고 어떤 때 사용하는지 구체적으로 생각해두고자 이번 글에서 이것저것 보면서 정리해두고자 합니다. 

### 1. qos는 왜 사용하는가?

 애플의 문서 [Prioritize Work with Quality of Service Classes](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/EnergyGuide-iOS/PrioritizeWorkWithQoS.html#//apple_ref/doc/uid/TP40015243-CH39-SW1)에 보면 qos를 왜 사용해야 하는지 잘 나와 있습니다. 빠르게 처리해야 하는 작업은 더 느리게 처리해도 되는 낮은 우선순위의 작업보다도 자원과 에너지를 더 많이 사용하게 됩니다. 따라서 한정된 자원과 에너지를 효율적으로 사용하기 위해서는 빠른 수행이 필요한 작업과 그렇지 않은 작업을 나누어 처리하는 qos가 필요하다고 할 수 있습니다. 

### 2. qos의 6가지 종류

qos는 6가지 종류가 있습니다. 위에서 아래로 갈 수록 중요도가 낮은 작업에 사용하기 좋습니다.

- `userInteractive`
- `userInitiated`
- 기본값 
- `utility`
- `background`
- `unspecified`

### 3. 각 qos의 쓰임

하나씩 차례로 살펴보겠습니다. 

#### `userInteractive`

 사용자와 직접적인 **상호작용**을 하는 작업에 사용하기 좋습니다. 예를 들어 **애니메이션이나 앱의 인터페이스를 업데이트 하는 등 UI 관련 이벤트를 처리**할 때 사용합니다. 유저와 상호작용을 하는 작업들을 처리하다 보니 작업을 처리하는데 드는 소요시간은 상당히 짧은 편입니다. 

#### `userInitiated`

사용자가 **initiated 한 뒤로 즉각적인 처리**가 이루어져야 하는 작업에 대해 사용합니다. **작업이 끝날 때까지 유저가 인터렉션을 할 수 없습니다.** 따라서 사용자에게 즉각적인 결과를 주어야 하지만 작업이 끝난 뒤에나 의미가 있다면 `userInitiated` qos를 사용하는 것이 효과적입니다. 예를 들어 **파일을 열거나, 유저가 유저인터페이스에서 무엇인가를 클릭한 뒤의 액션을 처리하거나 api로부터 데이터를 로딩하는 작업** 등을 수행할 수 있습니다.  소요시간은 몇초나 그것보다 적게 든다고 알려져 있습니다. 

#### `default`

qos를 선택하지 않으면 기본값으로 선택되는 qos입니다. `userInteractive` 와 `userInitiated` 보다는 중요도가 낮고, `utility`와 `background`보다는 높은 중요도를 갖습니다. 

#### `utility`

즉각적인 결과가 필요하지 않은 경우에 사용할 수 있습니다. 예를 들어 **데이터 다운로드 및 불러오거나 progress indicator와 함께 길게 실행되는 작업. 계산, I/O, networking** 등에서 사용합니다. 소요시간은 수초 에서 수분 단위로 든다고 알려져 있습니다. 

#### `background`

유저에게 직접적으로 보이지 않고 백그라운드에서 처리되는 작업의 경우에 사용하는 qos입니다. **데이터 미리 가져오기, 로컬 DB에 데이터를 저장하는 작업, 백업, 동기화** 등의 우선순위가 높지 않은 작업에 사용합니다. 속도보다는 에너지의 효율성을 고려하는 방식입니다. 아이폰의 저전력모드일 때 작업이 일시중단됩니다. 소요 시간은 수분에서 수시간이 걸린다고 알려져 있습니다. 

#### `unspecified`

> The absence of a quality-of-service class.

---

간단하게 DispatchQueue의 qos에 대해 알아보았습니다. 이것저것 찾아보다 보니 어떤 때에 qos를 맞추어 사용할 수 있는지 어느 정도 감이 오네요. 앞으로 더 효율적인 코드를 작성할 수 있길 바라면서 다음 글로 찾아오겠습니다. 이만 총총..



### References

- https://developer.apple.com/documentation/dispatch/dispatchqos
- https://www.swiftpal.io/articles/what-is-qos-quality-of-service-in-gcd-swift
- https://developer.apple.com/library/archive/documentation/Performance/Conceptual/EnergyGuide-iOS/PrioritizeWorkWithQoS.html#//apple_ref/doc/uid/TP40015243-CH39-SW1
- https://medium.com/the-traveled-ios-developers-guide/quality-of-service-849cd6dee1e


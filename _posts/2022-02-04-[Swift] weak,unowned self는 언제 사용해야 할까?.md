---
layout: single
title:  "[Swift] weak/unowned self는 언제 사용해야 할까?"
date:   2022-02-04 23:30:51 -0700
categories: jekyll update
---

## [Swift] weak/unowned self는 언제 사용해야 할까?
ARC에서 발생할 수 있는 Strong Reference Cycle로 인한 메모리 누수를 방지하기 위해 `weak self` 또는`unowned self` 를 사용하게 됩니다. 주로 클로저에서 weak self를 사용하고는 하는데 모든 클로저에 weak self를 사용해야 하는지, 그렇지 않다면 어떤 부분에 weak self를 사용해야 하는지 알아보고자 찾아본 내용을 정리하고자 글을 쓰게 되었습니다.

### Escaping, non escaping closure 여부

일반적으로 `@escaping` 키워드가 붙지 않은 클로저의 경우, 클로저의 scope 내에서 즉시 실행됩니다. 따라서 `weak`이나 `unowned` 를 사용할 필요가 없다고 하네요.

그러나 escaping closure의 경우 weak이나 unowned를 사용하지 않는 경우 다음과 같은 조건 하에서 메모리 누수가 발생할 수도 있다고 합니다.

- 한 프로퍼티에 저장되거나 다른 클로저로 넘길 때
- 클로저에 있는 객체가 해당 클로저나 넘겨진 클로저에서 strong reference를 지닐 때

따라서 escaping closure에서는 메모리 누수를 더욱 신경써야겠습니다.

### Delayed Deallocation

클로저가 메모리에서 해제되는 것이 지연될 때는 Strong Reference Cycle이 생기는 것은 아니지만, 지연된 해당 클로저로 인해 strong reference count가 올라간 상태이므로 해당 객체가 필요가 없어져도 메모리에 계속 살아있기 때문에 비효율적입니다. 이 경우 `[weak self]` 를 이용해  strong reference count를 올리지 않도록 해 메모리에서 해당 객체가 해제될 수 있도록 해야 한다고 합니다.

### 덧 ) weak self 사용 시 `guard let self = self else { return }` vs 그냥 `self?` 사용하는 것의 차이

`[weak self]` 를 사용하는 경우 `self` 가 optional 이기 때문에 주로 `guard let ~~` 를 사용해 옵셔널 바인딩을 해 self를 사용하거나 optional chaining을 해서 `self?.~~~~`  이런 식으로 클로저 내의 값을 처리하고는 합니다. 지금까지 두 가지 방법은 개인의 코드 스타일의 차이라고만 생각해 왔는데 자료를 찾아보다 보니 차이가 있다고 하네요.

- 옵셔널 바인딩으로 `guard let self = self else { return }` 을 사용하는 경우 self가 nil이 아닐 경우 Strong Reference Count를 올리는 self를 생성하게 됩니다. 이는 ARC에 따라서 클로저가 종료될 때까지 self가 살아있도록 합니다.
- 반면에 optional chaining으로 `self?.` 로 사용하는 경우 `self?`가 사용되는 모든 부분에서 각각 nil인지 체크를 하게 되고, nil일 경우에는 다음 줄로 그냥 넘어가게 됩니다. 그 말인즉슨 self가 이미 deallocated된 경우에 불필요한 작업을 하지 않을 수 있다는 것입니다.

ARC의 작동방식을 생각하면 당연한 것인데..생각하지 못했던 부분이네요. 따라서 지연된 해제(delayed deallocation)가 발생하는 클로저 등에서 메모리를 효율적으로 사용하기 위해서는 옵셔널 바인딩으로 사용하기보다는 옵셔널 체이닝으로 사용하는 것이 더 나을 것 같네요.

### References

- https://medium.com/@almalehdev/you-dont-always-need-weak-self-a778bec505ef

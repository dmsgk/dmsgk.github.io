---
title:  "[Swift] 메모리 참조, ARC, Strong Reference Cycle 이해하기"
date:   2021-09-01 11:11:51 +0900
categories: ios swift
---



안녕하세요 jalynne입니다. 이번 글에서는 Swift 언어에서 메모리를 관리하는 방식인 ARC에 대해 알아보고, 메모리 관리 시 주의해야 할 메모리 누수를 피하는 방법에 대해 자세히 알아보도록 하겠습니다. 



### 1. (일반론) Reference Counting - 메모리를 관리하는 방법 

 메모리는 한정적이므로 필요한 객체는 메모리에 올리고, 더 이상 사용하지 않는 객체는 메모리에서 해제하는 등의 메모리 관리가 필요합니다. 이럴 때 사용하는 방법 중 하나가 바로 **Referene Counting** 입니다. Reference Counting이라는 방법은 말 그대로 참조(reference)를 세서 해당 객체가 참조될 때 참조값을 늘리고, 더 이상 사용하지 않을 경우 참조값을 줄이는 방식을 사용하여 일반적으로 **참조값이 0이 될 때 해당 객체를 메모리에서 해제**합니다. 

Reference Counting은 강한 참조(String Reference)와 약한 참조(Weak Reference)로 나뉘는데 일반적으로 Reference Counting에 대해 이야기 할 때는 강한 참조를 사용한 counting을 의미합니다. 

### 2. ARC (Automatic Reference Counting) - Swift의 메모리 관리방법

Swift는 이러한 Reference Counting을 자동으로 해 주는 ARC를 사용합니다. **Swift는 컴파일 시에 컴파일러가 메모리에서 해제하는 코드를 적절히 넣어**줌으로써 개발자가 수동으로 메모리를 관리하지 않아도 되게 합니다. 

> <참고>
>
> Q. 가비지 컬렉터와 ARC의 차이점은 ?
>
> A. 가비지컬렉터는 컴파일이 끝나고 난 후 런타임에 메모리를 동적으로 감시하며 메모리를 관리하는 방식이라면, Swift의 ARC는 컴파일 시에 메모리를 관리하는 방법이라는 차이점이 있습니다. 

각 객체는 strong count, unowned count, weak count의 세 가지 reference count가 있습니다. strong, unowned는 항상 저장되고, weak reference count는 그 객체의 약한 참조가 처음 생성된 뒤에서야 저장됩니다.

Swift에서 제공한 예제 코드를 보면서 간단히 ARC가 동작하는 방식에 대해 이해해보도록 하겠습니다. 다음과 같은 `Person` 클래스가 있다고 할 때,

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

아래와 같은 `Person` 타입의 세 변수를 정의해 봅니다.  세 변수 모두 옵셔널이기 때문에 초기화는 `nil`로 되어 있고, 그렇기 때문에 지금은 `Person` 인스턴스를 참조하고 있지 않습니다.

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

새로운 `Person` 인스턴스를 만들어 `reference1` 변수에 할당하면 다음과 같이 프린트가 됩니다. 이는 initialization이 이루어졌다는 것을 의미하죠.

```swift
reference1 = Person(name: "John Appleseed") 
// Prints "John Appleseed is being initialized"
```

이렇게 된 이상 적어도 하나의 강한 참조(strong reference) 카운트가 있기 때문에 ARC는 `Person` 이 메모리에서 해제되지 않고 메모리에 있게 합니다. 

만일 같은 인스턴스를 `reference2`, `reference3` 에도 할당하게 된다면 강한 참조가 2만큼 더 counting됩니다. (총합 3)

```swift
reference2 = reference1
reference3 = reference1
```

그리고 앞서 3으로 만들었던 strong reference counts를 0으로 만들면 `Person` 인스턴스는 메모리에서 해제됩니다. 

```swift
reference1 = nil
reference2 = nil
reference3 = nil
// Prints "John Appleseed is being deinitialized"
```



### 3. ARC에서 주의해야 할 메모리 누수문제 - Strong Reference Cycle 

 ARC는 앞서 살펴본 것과 같이 프로그래머가 따로 메모리 관리를 위해 수동으로 코드를 작성하지 않아도 된다는 점에서 상당히 편리합니다. 그러나 한 가지 고려해야 할 점은 강한 참조로 인해 메모리 누수가 발생할 수 있다는 점입니다. 

일단 ARC가 어떤 인스턴스를 메모리에서 해제하기 위해서는 Strong Reference Count가 0이 되어야 합니다. 그러나 한 클래스의 인스턴스의 strong reference count가 **절대로 0이 되지 못하는 상황**이 발생한다면 메모리에서 해당 인스턴스를 해제할 수 없습니다.  이는 클래스 인스턴스 두 개가 서로에 대한 강한 참조를 갖고 있을 때 발생할 수 있고, 이를 **강한 참조 순환(String Reference Cycle)**이라고 합니다. 

역시 Swift문서의 예제코드를 보면서 이해해보도록 하겠습니다. 

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

 위 코드를 보면 `Person` 의 인스턴스는 `String` 타입의 `name` 프로퍼티와 `Optional` 타입의 `apartment` 프로퍼티를 갖습니다. 비슷하게 모든 `Apartment` 의 인스턴스는 `String` 타입의 `unit` 과 `Optional` 타입인 `tenant` 프로퍼티를 갖습니다. 

다음은 `john`과 `unit4A`라는 각각 `Person`과 `Apartment`에 설정될 옵셔널 변수입니다.

```swift
var john: Person?
var unit4A: Apartment?
```

여기에 새로운 인스턴스를 할당하여 메모리에 저장합니다. 

```swift
john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")
```

이 상태에서 강한 참조는 다음 그림과 같이 나타나게 됩니다. 

![../_images/referenceCycle01_2x.png](https://docs.swift.org/swift-book/_images/referenceCycle01_2x.png)



`Apartment`의 인스턴스의 프로퍼티 `tenant`가 `Person`이 될 수 있고, `Person` 인스턴스의 프로퍼티 `apartment`가 `Apartment` 타입이 될 수 있으므로, 이 둘을 아래와 같이 연결해줄 수 있습니다. 

```swift
john!.apartment = unit4A
unit4A!.tenant = john
```

그렇게 되면 아래 그림처럼 클래스 인스턴스 두 개 간의 양방향의 강한 참조, 즉 **강한 참조 사이클(strong reference cycle)**이 생기게 됩니다. 

![../_images/referenceCycle02_2x.png](https://docs.swift.org/swift-book/_images/referenceCycle02_2x.png)



따라서 `john`과 `unit4A`를 아래와 같이  `nil`값으로 바꾼다고 하더라도 서로의 인스턴스를 강한 참조하고 있기 때문에 reference count는 0이 될 수 없고 따라서 ARC가 두 인스턴스를 메모리에서 해제시키지 못해 메모리 누수가 발생하게 되는 것입니다. 

```swift
john = nil
unit4A = nil
```

![../_images/referenceCycle03_2x.png](https://docs.swift.org/swift-book/_images/referenceCycle03_2x.png)

아하..그렇군요..! 이제 Strong Reference Cycle을 이해하였습니다. Swift 문서 짱짱!



### 4. Strong Reference Cycle 문제를 예방하려면?

 클래스 인스턴스 간에 서로 강한 참조가 발생할 경우 강한 참조 순환 문제가 발생해 메모리 누수가 발생할 수 있다는 점을 알았습니다. 그렇다면 이 문제를 해결하려면 인스턴스 간에 서로 참조하는 관계가 발생할 경우 서로 강한 참조를 하는 대신, **약한 참조(weak reference) 또는 미소유 참조(unowned reference)**를 사용하여 문제를 해결할 수 있습니다. weak는 인스턴스를 참조하지만 strong과 달리 reference count를 증가시키지 않습니다. 

다른 인스턴스가 먼저 메모리에서 해제될 때에 해당 인스턴스에  `weak reference` 를 사용하고  다른 인스턴스가 같거나 긴 lifetime일 때 해당 인스턴스에 `unownede reference`를 사용하면 된다고 하네요.



앞서 살펴보았던 예제 코드도 `tenent ` 프로퍼티 앞에 `weak`을 적어주어 참조 형태를 변환함으로써 강한 참조 순환 문제를 해결할 수 있습니다. 

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    weak var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}

var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john	
```

![../_images/weakReference01_2x.png](https://docs.swift.org/swift-book/_images/weakReference01_2x.png)

---

 이해한 흐름 + 문서 순으로 정리해보았습니다. 정리해보니 Swift에서 메모리를 관리하는 ARC에 대해 조금이나마 이해하게 된 것 같네요. 앞으로 계속  ARC 관련해서 더 공부해봐야겠습니다. 그럼 이만 총총..





### References

- https://ko.wikipedia.org/wiki/%EC%B0%B8%EC%A1%B0_%ED%9A%9F%EC%88%98_%EA%B3%84%EC%82%B0_%EB%B0%A9%EC%8B%9D
- https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html
- https://stackoverflow.com/questions/24011575/what-is-the-difference-between-a-weak-reference-and-an-unowned-reference
- https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=hobin1019&logNo=221615212293
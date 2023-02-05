---
title: "[Swift] Actor type 알아보기"
date: 2023-01-25 23:45:51 +0900
categories: ios swift

---

​	

## Intro. 동시성 프로그래밍을 하면서의 어려움 - 경쟁상태의 발생

두 스레드가 동일한 데이터에 접근할 때 하나 이상이 쓰기 연산을 실행하면 data race 가 발생할 수 있다.

```swift
class Counter {
    var value = 0
    
    func increase() -> Int {
        value += 1
        return value
    }
}

let counter = Counter()

Task.detached {
    counter.increase()
}

Task.detached {
    counter.increase()
}
```

- 잘못된 방식!
- 작업 수행속도에 따라서 1, 2 를 얻게 될 수도, 2, 1을 얻게 될 수 있다. **심지어는 1과 1, 0과 0, 2와 2 라는 결과를 얻을 수도 있다.**

data races는 디버깅하기 어렵다.

- 여러 군데에서 접근하여 발생하는 문제이므로 `non local reasoning` (호출한 곳에서 바로 볼 수 있는 것이 아닌)을 요구하고,
- 운영체제의 스케줄러는 실시간으로 작업을 스케줄링하기 때문에 `nondeterministic` 하다.

data race는 shared mutable state에 의해 발생한다.

### data race를 피하는 방법

- value semantics를 활용해 shared mutable state를 제거하기
  - 값타입의 모든 mutation은 local이다.

```swift
var array1 = [1, 2]
var array2 = array1

array1.append(3)
array2.append(4)

print(array1) // [1, 2, 3]
print(array2) // [1, 2, 4]
```

각각의 copy 버전에서 각각의 연산한 값을 따로 갖는다. Swift의 Standard library의 대다수 -  Collection type(배열, 딕셔너리) 포함 -  value sementics를 갖는다.

맨 처음 예제를 다시 보자.

```swift
struct Counter {
    var value = 0
    
    mutating func increase() -> Int {
        value += 1
        return value
    }
}

let counter = Counter()

Task.detached {
    var counter = counter // local에서 var로 선언
    counter.increase()
}

Task.detached {
    var counter = counter 
    counter.increase()
}
```

이렇게 고치면 race condition은 해결되지만 value는 모두 1, 1이 나오게 되어 예상한 동작이 아니게 된다.

**동시성 프로그래밍에서 shared mutation state가 필요한 경우가 있다.**

### Shared mutable state in concurrent programs

- Shared mutable state는 동기화(synchronization)이 필요하다.
- 기존의 동기화를 위한 방법들
  - Atomics
  - Locks
  - Serial dispatch queues
- 기존의 방법은 각각 장점이 있지만 모두 사용할 때마다 정확한 원칙에 따라서 사용해야 한다는 단점이 있다.
- Actor는 여기서 등장!

## 1. Actor

- shared mutable state에 대한 동기화를 제공한다.
- actor의 상태를 나머지 프로그램과 분리(isolate)한다.
- 그 상태에 접근하는 유일한 방법은 actor를 통하는 것이다.
- actor를 통할 때마다 동기화 메커니즘이 상호배제적(mutually-exclustive)으로 상태에 접근할 수 있도록 보장한다.
- 다른 방식과 달리 Swift가 보장하는 동기화 방식이라는 점에서 차이가 있다.

### Actor type

- Swift에는 `Actor` 라는 새로운 타입이 생겼다. struct, enum, class와 유사하다.

  - property, method, init, subscript 등을 가질 수 있다.
  - protocol을 따를 수도 있고, extension 도 가능하다.
  
- 참조 타입이다.

- 데이터를 동기화하고 다른 프로그램과 분리하는 역할을 수행한다.

```swift
actor Counter {
    var value = 0
    
    func increase() -> Int {
        value += 1
        return value
    }
}

let counter = Counter()

Task.detached {
    print(counter.increase())
}

Task.detached {
		print(counter.increase())
}
```

한 메서드 호출은 다른 메서드 호출이 시작되기 전에 작업이 완료될 때까지 실행된다. 작업 수행 속도에 따라 1, 2 또는 2, 1을 갖게 되지만 동일한 카운트를 두번 얻거나 숫자를 건너 뛰는 일은 발생하지 않는다. 동시에 접근하려고 한다면 한 작업이 끝날 때까지 기다려야 한다.

그렇다면 두 번째 작업이 첫 번째 작업이 끝날 때까지 기다리는 것을 어떻게 보장할 수 있는가?

비동기 방식으로 actor에 접근할 때 actor가 바쁘면 suspended, 사용가능하면 resume. `await` 키워드를 사용해 나타낼 수 있음.

```swift
let counter = Counter()

Task.detached {
    print(await counter.increase())
}

Task.detached {
		print(await counter.increase())
}
```

### actor 내부의 synchronous interaction

```swift
extension Counter {
    func resetSlowly(to newValue: Int) {
        value = 0
        for _ in 0..< newValue {
            increment()
        }
        assert(value == newValue)
    }
}
```

**actor 내부의 호출 방식은 동기방식이다.** `await` 키워드를 사용할 필요가 없다.

synchronous code는 always runs to completione되고 uninterrupted된다. actor의 상태에 대해 동시성이 미치는 영향을 고려할 필요가 없다.

## 2. Actor reentrancy

### imagedownloader 예제

```swift
actor ImageDownloader {
    private var cache: [URL: Image] = [:]
    
    func image(from url: URL) async throws -> Image {
        if let cached = cache[url] {
            return cached
        }
        
        let image = try await downloadImage(from: url)
        
        cache[url] = image
        return image
    }
}
```

actor 를 사용하고 있기 때문에 동시성 프로그래밍에 따른 data races 걱정할 필요가 없다.

actor의 동기화 메커니즘은 하나의 작업만 캐시 인스턴스프로퍼티에 한 번 접근하는 코드를 실행하도록 보장하기 때문에 캐시가 변질될 걱정이 없다.

`await` 이 발생할 경우, 해당 함수가 여기서 suspended되어 있다는 것을 의미한다.

- 쉽게 그림으로 보기

  ![스크린샷 2023-01-24 오후 7.39.18](assets/스크린샷 2023-01-24 오후 7.39.18.png)

  동시성 태스크가 있다고 가정![스크린샷 2023-01-24 오후 7.40.22](assets/스크린샷 2023-01-24 오후 7.40.22.png)

  suspended 되어 있는 동안 서버에서 해당 url에 새로운 이미지를 배포했다고 가정하자.

  ![스크린샷 2023-01-24 오후 7.41.38.png](assets/스크린샷 2023-01-24 오후 7.40.42.png)

  이미지 캐시 확인 후, 아직 task1이 끝나지 않은 상태라 없으므로 suspended 된다.

  ![스크린샷 2023-01-24 오후 7.42.44.png](assets/스크린샷 2023-01-24 오후 7.41.38.png)

  ![스크린샷 2023-01-24 오후 7.43.10.png](assets/스크린샷 2023-01-24 오후 7.42.44.png)

  둘 중에 하나가 끝났으면, - task1이라 가정 - 끝나고 작업을 계속한다.

  ![스크린샷 2023-01-24 오후 7.44.18](assets/스크린샷 2023-01-24 오후 7.44.18.png)

  cache를 populate 하고

  ![스크린샷 2023-01-24 오후 7.45.07.png](assets/스크린샷 2023-01-24 오후 7.45.07.png)

  다른 작업이 끝나면 캐시 값을 덮어쓴다.

  이렇게 되면 캐시값이 있을 때 동일한 이미지를 얻을 것이라고 기대한 것과 다르게 작동하고 user interface를 캐시를 지우지 않고도 일관성 있게 유지할 수 없다.

  이를 해결하기 위해서는 다음과 같이 수정

  ```swift
  actor ImageDownloader {
      private var cache: [URL: Image] = [:]
      
      func image(from url: URL) async throws -> Image {
          if let cached = cache[url] {
              return cached
          }
          
          let image = try await downloadImage(from: url)
          
          cache[url] = cache[url, default: image]
          return image
      }
  } 
  ```

**Actor reentrancy 정리**

- actor의 state가 suspension 동안 변할 수 있다는 것을 예상해야 한다.
- `await` 이후에 assumption을 체크해야 한다.

## 3. Actor isolation

isolation은 actor가 mutable state를 보전하는 방법이다. mutable state를 보전하는 주요 방법은 stored instance property를 `self` 를 통해서만 접근할 수 있도록 하는 것이다.

### protocol conformance - must respect actor isolation

```swift
actor LibraryAccount {
    let idNumber: Int
    var booksOnLoan: [Book] = []
}

extension LibraryAccount: Hashable {
    func hash(into hasher: inout Hasher) {
        hasher.combine(idNumber)
    }
}
```

만약 LibraryAccunt 가 Hashable 프로토콜을 준수한다고 할 때, 이 함수는 actor 밖에서 불릴 수 있다. 그러나 hash(into)는 비동기가 아니기 때문에 actor의 isolation을 유지할 방법이 없다.

⇒ 해결방안: `nonisolated` 키워드 사용할 것

```swift
actor LibraryAccount {
    let idNumber: Int
    var booksOnLoan: [Book] = []
}

extension LibraryAccount: Hashable {
    nonisolated func hash(into hasher: inout Hasher) {
        hasher.combine(idNumber)
    }
}
```

- 문법적으로 actor 내부에 기술되어 있지만 actor 외부에서 처리될 수 있는 내용라는 것을 표시한다.
- actor의 mutable state를 참조할 수 없다. 만약 해시함수 내부에서 booksOnLoan을 사용한다면 에러

### closures - isolated 될 수도, 그렇지 않을 수도

```swift
extension LibraryAccount {
    func readSome(_ book: Book) -> Int {
        // ...
    }
    
    func read() -> Int {
        booksOnLoan.reduce(0) { book in
            readSome(book)
        }
    }
}
```

→ isolated

```swift
extension LibraryAccount {
    func read() -> Int {
        // ...
    }
    
    func readLater() {
        Task.detached {
            await self.read()
        }
    }
}
```

→ non-isolated

### Sendable types

값 타입이 아닌, 참조타입을 사용해 값을 전달할 경우, actor 밖에서 연산이 일어나는 경우에는 data race를 초래할 가능성이 있다.

동시성 프로그래밍 상에서 데이터를 안전하게 보낼 수 있는 타입: `Sendable`

- 각각 카피를 뜨면, 서로 간섭 없이 각자의 카피를 수정한다.
- Sendable type인 것
  - value type
  - actor type
  - Immutable classes
  - Internally-synchronized class
  - `@Sendable` func type

```swift
static func detached(operaion: @Sendable () async -> Success) -> Task<Success, Never> {
        
    }

var counter = Counter()

Task.detached {
    print(counter.increase()) // 컴파일 에러
}

Task.detached {
    print(counter.increase()) // 컴파일 에러
}
```

## 4. Main actor

- 메인스레드에서 돌아가도록 하는 singleton actor

## References

- https://github.com/apple/swift-evolution/blob/main/proposals/0306-actors.md#actor-reentrancy
- https://developer.apple.com/videos/play/wwdc2021/10133

- https://developer.apple.com/documentation/swift/sendable
- https://developer.apple.com/documentation/swift/anyactor
- https://developer.apple.com/documentation/swift/concurrency
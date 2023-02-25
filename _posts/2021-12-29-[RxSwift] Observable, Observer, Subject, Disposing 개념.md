---
title:  "[RxSwift] Observable, Observer, Subject, Disposing 개념"
date:   2021-12-29 10:30:51 +0900
categories: iOS framework
---



### Observable

Observable 타입은 비동기적으로 다음과 같은 세 가지 타입의 이벤트를 방출(emit)합니다. 

- `next` : 최신(또는 다음) 데이터 값을 들고 있는 이벤트를 의미합니다. 이 경우 Observer가 값을 받을 수 있게 됩니다. 
- `completed` : 성공적으로 이벤트 시퀀스가 끝났을 때. 이 경우 해당 시퀀스는 terminate됩니다. 
- `error`  : 해당 Observable이 에러가 발생한 경우, 해당 시퀀스는 terminate됩니다. 

> 참고 - RxSwift Event관련 소스코드
>
> ```swift
> /// Represents a sequence event.
> ///
> /// Sequence grammar:
> /// **next\* (error | completed)**
> public enum Event<Element> {
>     /// Next element is produced.
>     case next(Element)
> 
>     /// Sequence terminated with an error.
>     case error(Swift.Error)
> 
>     /// Sequence completed successfully.
>     case completed
> }
> ```

Observable은 `.subscribe` 를 한 뒤부터 이벤트를 보내기 시작합니다.  Observable 타입은 subscription을 받기 전까지는 아무 것도 하지 않습니다. 

### Observer

Observable에서 방출한 이벤트를 받는 역할을 합니다. 

### Subject

Subject 타입은 **Observable 타입과 Observer 타입의 역할을 같이 수행**할 수 있는 타입을 의미합니다. RxSwift에는 다음과 같은 4가지 Subject가 있습니다.

- `PublishSubject`: Starts empty and only emits new elements to subscribers. Combine의 `PassthoroughSubject` 과 비슷한 개념인 것 같네요. 
- `BehaviorSubject`: Starts with an initial value and replays it or the latest element to new subscribers. 이 역시 Combine의 `CurrentValueSubject`과 비슷한 개념인 것 같습니다. 
- `ReplaySubject`: Initialized with a buffer size and will maintain a buffer of elements up to that size and replay it to new subscribers.
- `AsyncSubject`: `completed` 이벤트를 받았을 때에만 가장 마지막 `next` 이벤트를 방출한다고 합니다. 그렇지만 좀처럼 쓰지 않는다고 합니다

### Disposing

Disposing은 Observed sequence를 종료시킬 수 있는 또 다른 방법입니다. 주로 `DisposeBag` 을 사용해 여러 subscription을 담아 두었다가 해당 스트림이 필요 없어질 경우(관련 객체가 메모리에서 해제되는 경우 등)  스트림을 종료시키게 됩니다. 



### References

- https://github.com/ReactiveX/RxSwift
- https://www.raywenderlich.com/books/rxswift-reactive-programming-with-swift/v4.0/chapters/1-hello-rxswift#toc-chapter-005-anchor-001


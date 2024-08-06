---
title: "[Swift] existential type 개념과 Swift 5.7 이후의 변경사항"
date: 2024-08-06 10:08:21 +0900
categories: 
    - iOS 
    - Swift

---



## 1. Existential type 개념

protocol 선언 후 채택하여 함수를 사용한다고 생각해 보자.

```swift
protocol Testable {
		func test()
}

struct A: Testable {
		func test() {
				print("A Test")
		}
}

struct B: Testable {
		func test() {
				// 테스트
		}
}
```

만일 타입과 상관없이 동일하게 동작하는 함수를 사용하고 싶다면 두 가지 방법이 있다. 첫째, Generic 을 활용한다. 둘째, existential type 을 활용한다.

```swift
// generic 활용
func test<T: Testable>(with test: T) {
		test.test()
}

// existential type 을 활용
func test(with test: any Testable) { 
		test.test()
}

var test: any Testable = A()
```

existential type 은 이와 같이 protocol이 타입으로써 사용될 때, 그 타입을 의미한다.

↔ 반대되는 개념: concrete type

## 2. Swift 5.7 이후의 변화

[swift-evolution/proposals/0335-existential-any.md at main · swiftlang/swift-evolution](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0335-existential-any.md#grammar-of-explicit-existential-types)

기존에는 타입을 선언할 때 protocol 을 채택하든, concrete type 이라고 쓰든 코드만 보고 알 수가 없었다.

Swift 5.7 부터는 이를 구분하기 위해 existential type 에는 `any` 키워드를 붙여서 구분이 가능하게끔 컴파일러가 강제한다.

any 키워드를 사용 시의 장점: type-safety

[What's the 'any' keyword? Understanding Type Erasure in Swift](https://swiftrocks.com/whats-any-understanding-type-erasure-in-swift)

5.7 이후에는 `any` 키워드를 써서 existential type 을 만들어 사용 시 컴파일러가 type-safety 를 보장한다.

다음과 같은 protocol 이 있다고 하자.

```swift
protocol Animal {
    associatedtype Food
    func eat(_ food: Food)
}

protocol FoodProvider {
    func getFood<T: Animal>(for animal: T) -> T.Food
}
```

만약 여러 동물이 있어 다 모아 두고 밥을 주는 기능이 필요하다고 한다면, 기존에는 다음과 같이 함수를 만들었을 것이다.

```swift
func feedAll(_ animals: [Animal], provider: FoodProvider) {
    animals.forEach {
        let food = provider.getFood(for: $0)
        $0.eat(food)
    }
} 
```

Swift 5.7 부터는 에러를 방출한다.

이 상황을 해결하기 위해서 Type 을 지운 다음과 같은 `AnyAnimal` 타입을 만들어서 사용할 수도 있다.

```swift
final class AnyAnimal {
    var getFood: (FoodProvider) -> Any
    var eatFood: (Any) -> Void

    init<T: Animal>(_ animal: T) {
        self.getFood = { provider in
            provider.getFood(for: animal)
        }
        self.eatFood = { food in
            animal.eat(food as! T.Food)
        }
    }
}

func feedAll(_ animals: [AnyAnimal], provider: FoodProvider) {
    animals.forEach {
        let food = $0.getFood(provider)
        $0.eatFood(food)
    }
}
```

이렇게 하면 해결이 가능하지만, 여기에는 문제가 있다. FoodProvider 에서 제공하는 food 타입이 Animal 에서 제공하는 Food 타입으로 캐스팅이 되지 않는다면? 크래시가 발생한다. (근데 그런 상황이 발생할 수가 있나?)

지선생님의 도움을 받았다.

```swift
struct DogFood {
    let name: String
}

struct CatFood {
    let name: String
}

struct Dog: Animal {
    typealias Food = DogFood
    
    func eat(_ food: DogFood) {
        print("Dog is eating \\(food.name)")
    }
}

struct Cat: Animal {
    typealias Food = CatFood
    
    func eat(_ food: CatFood) {
        print("Cat is eating \\(food.name)")
    }
}

// -- 잘못된 예시
class SimpleFoodProvider: FoodProvider {
    func getFood<T: Animal>(for animal: T) -> T.Food {
        // 잘못된 타입의 음식을 제공
        return CatFood(name: "Cat Food") as! T.Food
    }
}

let dog = Dog()
let dogWrapper = AnyAnimal(dog)
let animals: [AnyAnimal] = [dogWrapper]
let provider = SimpleFoodProvider()

feedAll(animals, provider: provider)  // 여기서 크래시 발생
```

이런 경우 컴파일러는 알 수가 없다.

5.7 이후로 any 를 사용하여 다음과 같이 사용하면, 컴파일러가 for 문을 돌 때마다 각자의 concrete type 에 맞는 값을 얻을 수 있게 되어 컴파일 단에서 타입안정성을 얻을 수 있다.

```swift
func feedAll(_ animals: [any Animal], provider: FoodProvider) {
    animals.forEach {
        feed($0, provider: provider) // Now works!
    }
}

func feed<T: Animal>(_ animal: T, provider: FoodProvider) {
    let food = provider.getFood(for: animal)
    animal.eat(food)
}
```

## References

- [Using existentials and generics | Apple Developer Documentation](https://developer.apple.com/tutorials/app-dev-training/using-existentials-and-generics)

- [[Swift\] Swift 5.6 의 Existential any](https://sujinnaljin.medium.com/swift-swift-5-6-의-existential-any-ef0ce6bc7bc2)

- [swift-evolution/proposals/0335-existential-any.md at main · swiftlang/swift-evolution](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0335-existential-any.md#grammar-of-explicit-existential-types)
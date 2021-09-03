# [Episode #1 Functions](https://www.pointfree.co/episodes/ep1-functions)
- function을 정의.
```Swift
func incr(_ x: Int) -> Int
func square(_ x: Int) -> Int
```
- Custom operater 정의.
```Swift
precedencegroup ForwardApplication {
    associativity: left   
}

infix operator |>: ForwardApplication

func |> <A, B>(a: A, f(A) -> B) -> B {
    return f(a)
}

precedencegroup ForwardComposition {
  associativity: left
  higherThan: ForwardApplication
}

infix operator >>>: ForwardComposition

func >>> <A, B, C>(f: @escaping (A) -> B, g: @escaping (B) -> C) -> ((A) -> C) {
    return { a in 
        g(f(a))
    }
}
```
- Method Composition
```Swift
String(2.incr().square())
2 |> incr >>> square >>> String.init

[1, 2, 3]
    .map { $0 + 1 }
    .map { $0 * $0 }
[1, 2, 3]
    .map(incr)
    .map(square)
[1, 2, 3]
    .map(incr >>> square)
```
- Don't fear the function. 
- 반드시 global namespace여야할 필요도 없고. type이나 module 로 범위를 제한할 수도 있다.

# [Episode #2 Side Effects](https://www.pointfree.co/episodes/ep2-side-effects)
- Side Effects는 왜 테스트하기 힘든가.
- Side Effect가 없는 함수를 먼저 살펴보자. 같은 input을 넣으면 언제나 같은 output이 나온다. Consistant 하다.
- Side Effect가 있는 함수. 함수 내에서 print만 해도 Side Effect가 존재하는 것이다.
- Promise가 있었다면 input/return 타입에 상관없이 함수들을 연결할 수 있었겠지만...
- 없으니까... 2개의 input parameter가 필요한 함수를 파라미터 1개로 바꾸고, 나머지를 input으로 받는 함수를 return하도록 수정.
- Reference type을 사용하면, mutation의 내부 구현을 알아야만 한다.
- Value type을 사용하면 이를 통제할 수 있고, value type의 mutation이 필요한 경우, inout 키워드를 사용하는 것도 가능. 이 때 &를 사용해야 함.
- Function Composition을 위해 operator를 계속 정의해나가는.

# [Episode #3 UIKit Styling with Functions](https://www.pointfree.co/episodes/ep3-uikit-styling-with-functions)
- More real world example.
- UIAppearance. 자신의 style만 변경 가능하지, child view의 appearance를 함께 수정할 수는 없음.
- BaseButton, FilledButton, RoundedButton으로 상속 구조를 구현했을 때, Filled와 Rounded를 함께 적용하고 싶은 경우 어떻게 해야 하는가.
- UIButton을 파라미터로 받는 함수를 스타일별로 만들어서 필요에 따라 호출을 이어가는 방법.
- 각 스타일을 변경하는 함수를 리턴하는 함수를 만든 후, Function Composition하는 방법.

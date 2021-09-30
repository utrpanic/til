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

# [Episode #4 Algebraic Data Types](https://www.pointfree.co/episodes/ep4-algebraic-data-types)
- Algebra? 너무 오랫만에 듣는 단어...
- fatalError() returns Never. Never는 대체 무엇인가. 이름 그대로 Never.
- Generic으로 만든 struct Pair<A, B>와 enum Either<A, B>. 해당 타입의 값의 종류는 모두 몇 개인가. (이걸 아는 게 무슨 의미인거지?)
- Void는 실제 타입이 아닌 empty tuple의 typealise. 즉, extension 같은 것이 불가능.
```Swift
sum([1, 2]) + sum([]) == sum([1, 2])
product([1, 2]) * product([]) == product([1, 2])
```
- URLSession의 completionHandler. (Data?, URLSession?, Error?). 불가능한 조합이 존재한다. 예를 들어 셋다 non-optional 일 수는 없는.
- Either<Pair<Data, URLResponse>, Error> 또는 Result<(Data, URLResponse), Error>

# [Episode #5 Higher-Order Functions](https://www.pointfree.co/episodes/ep5-higher-order-functions)
```Swift
func curry<A, B, C>(_ f: @escaping (A, B) -> C) -> (A) -> (B) -> C {
    return { a in { b in f(a, b) } }
}

func flip<A, B, C>(_ f: @escaping (A) -> (B) -> C) -> (B) -> (A) -> C {
    return { b in { a in f(a)(b) } }
}

let stringWithEncoding = flip(curry(String.init(data:encoding:)))
let utf8String = stringWithEncoding(.utf8)
```
- 잘 이해가 안되는데... 이걸 해서 얻을 수 있는 효과가 무엇일까?
- 모든 instance 함수에 숨겨진 static 함수가 있다고.
```Swift
"Hello".uppercased(with: Locale.init(identifier: "en"))
String.uppercased(with:)("Hello")(Locale.init(identifier: "en"))
```
- 데이터 파이프라인 이야기가 나오는데, 하나를 받고 하나를 리턴하는 것인가. 함수 컴포지션이 더 자유로워진다?

# [Episode #6 Functional Setters](https://www.pointfree.co/episodes/ep6-functional-setters)
- Complicated, deeply nested data structure가 있을 때, 그 일부를 변경하고 싶다면. Simple, clean, composable way는 어떤 것이 있는가?
```Swift
precedencegroup BackwardsComposition {
    associativity: left
}

infix operator <<<: BackwardsComposition

func <<< <A, B, C>(f: @escaping (B) -> C, g: @escaping (A) -> B) -> ((A) -> C) {
    return { a in 
        f(g(a))
    }
}
```
- What's the point? 잘 모르겠는데! Imperative data transforming?

# [Episode #7 Setters and Key Paths](https://www.pointfree.co/episodes/ep7-setters-and-key-paths)
- Key Path는 다른 언어와 비교해봐도 Swift의 독특한 부분이라고.
- Composable Setter World!
- 봐도 이해가 잘;;; 몇 번 더 봐야할 듯. 그리고... global 함수만 다루고 있는데, instance 함수도 동일하게 넘기면 되는가.
- Exercise가 따로 추가로 있는 줄은 몰랐네. 더 해봐야할 듯.

# [Episode #8 Getters and Key Paths](https://www.pointfree.co/episodes/ep8-getters-and-key-paths)
- KeyPath를 사용하는 higher order function을 일일이 정의하기보다는 KeyPath를 받는 function을 만들어서 넘긴다면 기존의 map, filter 등을 그대로 사용할 수 있다.
- KeyPath를 사용하면 nested structure에 대해서도 쉽게 함수를 만들어낼 수 있음.
- Function composition이 왜 좋은지 여전히 이해 못한.

# [Episode #9 Algebraic Data Types: Exponents](https://www.pointfree.co/episodes/ep9-algebraic-data-types-exponents)
- URLSession의 completionHandler. 그 조합을 보면 얼마나 이상한가. Data와 Error는 mutually exclusive.
- Result<Data, Error>를 사용하면? 타입이 곧 문서나 다름없다.
- Never도 enum이라구! switch에 넣어보면 case를 정의하지 않아도 컴파일이 된다. -> 황당하게도... never를 switch에 넣으면 리턴을 하지 않아도 함수가 컴파일된다. 신기...
- What makes your functions more complicated.
- 하지만 pow 함수를 이용한 공식을 type으로 맵핑했다가 함수로 변경하면서 설명하고자하는 부분을 이해하지 못하고 있음. 재미야 있지만...

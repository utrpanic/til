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

# [Episode #10 A Tale of Two Flat‑Maps](https://www.pointfree.co/episodes/ep10-a-tale-of-two-flat-maps)
- 중첩된 배열을 flatten. compactMap은 왜 나왔는가. 동작이 다른가.
```Swift
struct User {
  let name: String?
}
let users = [User(name: "Blob"), User(name: "Math")]
users
  .map { $0.name }
// [{some "Blob"}, {some "Math"}]
users
  .flatMap { $0.name }
// ["Blob", "Math"]

struct NamedUser {
  let name: String
}
let namedUsers = [NamedUser(name: "Blob"), NamedUser(name: "Math")]
namedUsers
  .map { $0.name }
// ["Blob", "Math"]
namedUsers
  .flatMap { $0.name }
// ["B", "l", "o", "b", "M", "a", "t", "h"]
```
- What's the point? 영상 말미마다 계속 이야기하는데 제대로 이해를 못하고 있는 것 같음. 좀 더 참고 계속 진도를 나가야할까.

# [Episode #11 Composition without Operators](https://www.pointfree.co/episodes/ep11-composition-without-operators)
- func으로 custom operator를 대신하려고 해도, 파라미터를 갯수를 동적으로 늘릴 수 없는 문제가 있다. overload로 대응할 수는 있겠지만...
```Swift 
func with<A, B>(_ a: A, _ f: (A) -> B) -> B {
  return f(a)
}
func pipe<A, B, C>(_ f: @escaping (A) -> B, _ g: @escaping (B) -> C) -> (A) -> C {
  return { g(f($0)) }
}
```
- VArgs를 사용하면 가능. 그런데... generic에도 vargs 사용 가능한건가?
- What's the point? Composition이 중요하다. 우린 이 custom operator들을 사랑하지만 그것들을 코드 베이스에 갑자기 도입하는 게 어려울 수도 있기 때문에 human name을 지은 것이다.

# [Episode #12 Tagged](https://www.pointfree.co/episodes/ep12-tagged)
- Email이나 ID 같은 프로퍼티를 무조건 String으로만 정의할 것인가.
- RawRepresentable을 conform하면, singleValueContainer를 통한 decode를 기본으로 제공함.
```Swift
struct Email: Decodable, RawRepresentable {
  let rawValue: String
}
```
- 여기에 Equatable을 conform하면, value와 optional을 ==로 비교할 수 있다?
- 그럼 Decodable, RawRepresentable, Equatable을 매번 conform해야하는가.
```Swift
struct Tagged<Tag, RawValue> {
  let rawValue: RawValue
}
extension Tagged: Decodable where RawValue: Decodable {
  init(from decoder: Decoder) throws {
    let container = try decoder.singleValueContainer()
    self.init(rawValue: try container.decode(RawValue.self))
  }
}
extension Tagged: Equatable where RawValue: Equatable {
  static func == (lhs: Tagged, rhs: Tagged) -> Bool {
    return lhs.rawValue == rhs.rawValue
  }
}
```
- 하지만 매번 해당 객체를 생성하는 것도 너무 번거롭기 때문에... 와 진짜 근사하다.
```Swift
extension Tagged: ExpressibleByIntegerLiteral where RawValue: ExpressibleByIntegerLiteral {
  typealias IntegerLiteralType = RawValue.IntegerLiteralType
  init(integerLiteral value: IntegerLiteralType) {
    self.init(rawValue: RawValue(integerLiteral: value))
  }
}
```
- What's the point? 훨씬 type safe하고 readable. 그동안 못해서 안한 거지... Swift 4.1이 이정도로 지원해주는데, 사용하지 않을 이유가 있는가.

# [Episode #13 The Many Faces of Map](https://www.pointfree.co/episodes/ep13-the-many-faces-of-map)
- 언어에 map()만 있으면 함수형 컨셉을 지원하는 것인가.
- map()을 다양하게 구현하는데, 각 구현체들이 unique map 인지를 판단하는 것은 무엇을 위한 것인가. (파라미터로 identity 함수를 넣을 경우, map이 자신을 그대로 리턴하는가. map(id) == id)
- Map means a Functor!
- 우리가 정의한 타입에 대해 map을 작성하는 것을 두려워하지 말아야한다.

# [Episode #14 Contravariance](https://www.pointfree.co/episodes/ep14-contravariance)
- Other side of map()
- NSObject > UIResponder > UIView > UIControl > UIButton
- (UIView) -> UIView as (UIButton) -> UIView
- (UIView) -> UIView as (UIView) -> UIResponder
- Subtyping a function by its output is a *covariant* relationship, and subtyping a function by its input is a *contravariant* relationship.
```
// (A) -> ((B) -> C) -> D)
//         |_|   |_|
//         -1    +1
//         |_______|   |_|
//            -1       +1
// |_|    |______________|
// -1           +1
// A와 C는 -1(contravariant), B와 D는 +1(covariant).
```
- 하지만 이게 여전히 무슨 의미인지 모르겠네.

# [Episode #15 Setters: Ergonomics & Performance](https://www.pointfree.co/episodes/ep15-setters-ergonomics-performance)
```Swift
func prop<Root, Value>(_ kp: WritableKeyPath<Root, Value>)
  -> (@escaping (Value) -> Value)
  -> (Root) -> Root {
    return { update in
      { root in
        var copy = root
        copy[keyPath: kp] = update(copy[keyPath: kp])
        return copy
      }
    }
}

func prop<Root, Value>(
  _ kp: WritableKeyPath<Root, Value>,
  _ f: @escaping (Value) -> Value
  )
  -> (Root) -> Root {
    return prop(kp)(f)
}

func over<Root, Value>(
  _ setter: (@escaping (Value) -> Value) -> (Root) -> Root,
  _ f: @escaping (Value) -> Value
  ) -> (Root) -> Root {
    return setter(f)
  }
)

func set<Root, Value>(
  _ setter: (@escaping (Value) -> Value) -> (Root) -> Root,
  _ f: @escaping (Root) -> Root
  ) -> (Root) -> Root {
    return over(setter, { _ in value })
  }
)
```
- 점점 모르게 되는 느낌.
- |>, <>, >>>, <<<의 의미를 다시 짚어봐야할 것 같다.

# [Episode #16 Dependency Injection Made Easy](https://www.pointfree.co/episodes/ep16-dependency-injection-made-easy)
- 설명을 쉽게 위해서 PlaygroundSupport를 사용하는 것 같기도 한데, 실제로 View 구현할 때 사용하는 것도 좋을 것 같다.
- Mock을 사용하면 많은 boilerplate가 필요하다. 생성자, 프로토콜, 주입 코드 등.
- Current라는 global variable을 정의하고, 여기에 실제 구현체와 mock을 바꿔넣는 방법으로 생성자 관련 코드를 생략할 수 있다.
- protocol의 function을 function variable로 변경하면 프로토콜 관련 코드를 생략할 수 있다.

# [Episode #17 Styling with Overture](https://www.pointfree.co/episodes/ep17-styling-with-overture)
- https://github.com/pointfreeco/swift-overture
- Overture: 1. 서곡 2. 제의하다 3. ...을 말을 꺼내다.

# [Episode #18 Dependency Injection Made Comfortable](https://www.pointfree.co/episodes/ep18-dependency-injection-made-comfortable)
- A lot of boilerplate.
- `Current`라는 Environment 객체를 global singleton으로 정의해서 개선해보았다. Global singleton에 마음이 불편할 수 있다고.
- Mock 객체를 변형하는데 Overture도 사용하고...

# [Episode #19 Algebraic Data Types: Generics and Recursion](https://www.pointfree.co/episodes/ep19-algebraic-data-types-generics-and-recursion)
- Recursive data type. Swift에서는 이를 위해 indirect 키워드를 사용해야 한다.
```Swift
enum List<A> {
  case empty
  indirect case cons(A, List<A>)
}
enum AlgebraicList<A> {
  case empty
  case one(A)
  case two(A, A)
  case three(A, A, A)
  case four(A, A, A, A)
  // ...
}
struct NonEmptyArray<A> {
  private let values: [A]
  init?(_ values: [A]) {
    guard !values.isEmpty else { return nil }
    self.values = values
  }
  init(values: A...) {
    self.values = values
  }
  var first: A {
    return self.values.first!
  }
}
// struct NonEmptyList<A> {
//   let head: A
//   let tail: List<A>
// }
enum NonEmptyList<A> {
  case singleton(A)
  indirect case cons(A, NonEmptyList<A>)
  var first: A {
    switch self {
      case let .singleton(first): return first
      case let .cons(head, _): return head
    }
  }
}
```
- first를 non-optional로 받기 위한 긴 여정. Algebraic Data Type을 정의.

# [Episode #20 NonEmpty](https://www.pointfree.co/episodes/ep20-nonempty)
- first를 non-optiona로. Collection protocol을 conform할 때, optional property를 override할 때 non-optional로 override하는 것이 가능하다.
- NonEmpty를 만드는 데 언급된 protocol/generic 들.
  - CustomStringConvertible
  - Collection
  - BidirectionalCollection
  - Collection.Element
  - Collection.Index
  - RangeReplaceableCollection
  - Comparable
  - MutableCollection
  - SetAlgebra

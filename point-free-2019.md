# [Episode #42 The Many Faces of Flat‑Map: Part 1](https://www.pointfree.co/episodes/ep42-the-many-faces-of-flat-map-part-1)
- Map이나 Zip을 쓸 수도 있겠지만...
- Nesting problem. map 블록 내에서 map을 반복할 때마다.
- 그래서 flatMap.

# [Episode #43 The Many Faces of Flat‑Map: Part 2](https://www.pointfree.co/episodes/ep43-the-many-faces-of-flat-map-part-2)
```
// Array 
// map:        ((A)    ->  C ) -> (([A])      -> [C])
// zip(with:): ((A, B) ->  C ) -> (([A], [B]) -> [C])
// flatMap:    ((A)    -> [C]) -> (([A])      -> [C])

// Optional
// map:        ((A)    -> C ) -> ((A?)     -> C?)
// zip(with:): ((A, B) -> C ) -> ((A?, B?) -> C?)
// flatMap:    ((A)    -> C?) -> ((A?)     -> C?)

// Result
// map:        ((A)    ->        C    ) -> ((Result<A, E>)               -> Result<C, E>)
// zip(with:): ((A, B) ->        C    ) -> ((Result<A, E>, Result<B, E>) -> Result<C, E>)
// flatMap:    ((A)    -> Result<C, E>) -> ((Result<A, E>)   
```
- map: Purely transform the underlying type of your generic container.
- zip: Combine multiple generic container values into a single generic container.
- flatMap: Sequence your generic containers.

# [Episode #44 The Many Faces of Flat‑Map: Part 3](https://www.pointfree.co/episodes/ep44-the-many-faces-of-flat-map-part-3)
- `zip` combine multiple independent values into single one.
- `map` pure infailable transformation.
- `flatMap` failable transformation.
- nil handling, error handling, `Valided`, `Func`, `Parallel`에도 동일하게 응용 가능.
```Swift
zip(with: UserEnvelope.init)(
  Result { try requireSome(Bundle.main.path(forResource: "user", ofType: "json")) }
    .map(URL.init(fileURLWithPath:))
    .flatMap { url in Result { try Data.init(contentsOf: url) } }
    .flatMap { data in Result { try JSONDecoder().decode(User.self, from: data) } },

  Result { try requireSome(Bundle.main.path(forResource: "invoices", ofType: "json")) }
    .map(URL.init(fileURLWithPath:))
    .flatMap { url in Result { try Data.init(contentsOf: url) } }
    .flatMap { data in Result { try JSONDecoder().decode([Invoice].self, from: data) } }
)
// Result<UserEnvelope, Swift.Error>
```

# [Episode #45 The Many Faces of Flat‑Map: Part 4](https://www.pointfree.co/episodes/ep45-the-many-faces-of-flat-map-part-4)
- `Parallel`의 `flatMap`을 `then`으로 rename할 수 있다. flatMap 같은 기능이 다른 이름으로 제공되는 것은 흔한 구현이라고.
```Swift 
Parallel { f in f(Bundle.main.path(forResource: "user", ofType: "json")!) }
  .then(URL.init(fileURLWithPath:))
  .then { url in Parallel { f in f(try! Data(contentsOf: url) } }
  .then { data in Parallel { f in f(try! JSONDecoder().decode(User.self, from: data)) } }
```
  - By using this alternative name we lose out on a vast body of literature that uses the term `flatMap`.
  - And why rename `flatMap` to `then` for only `Parallel`? Should we also use it for optionals, arrays, results, validated, and func? `flatMap` works in all of those cases so is it worth using a specialized name?
  - The biggest problem with this rename is that it is often accompanied with other renames.
- Kotlin이나 TypeScript에서 `Optional`은 타입이 아니라 컴파일러의 기능으로 제공된다고. 즉 flatten은 자동으로 처리함. Nullability가 중첩되는 일이 없음.
- 읽고도 알쏭달쏭... `flatMap`이라는 용어를 사용함으로써 얻어지는 직관을 포기할 수 없다고 하는 듯 한데, `then`은 어째서?

# [Episode #46 The Many Faces of Flat‑Map: Part 5](https://www.pointfree.co/episodes/ep46-the-many-faces-of-flat-map-part-5)
- `flatMap`을 이용해서 `map`과 `zip`을 구현할 수 있는가.
```Swift
func map<A, B, E>(
  _ f: @escaping (A) -> B
  ) -> (Parallel<Result<A, E>>) -> Parallel<Result<B, E>> {

  return { parallelResultA in
    parallelResultA.map { resultA in
      resultA.map { a in
        f(a)
      }
    }
  }
}
```
- `Parallel`과 `Result`가 모두 `map`을 제공하기 때문에, general하게 구현 가능.
```Swift
func zip<A, B, E>(
  _ lhs: Parallel<Result<A, E>>,
  _ rhs: Parallel<Result<B, E>>
  ) -> Parallel<Result<(A, B), E>> {

  return zip(lhs, rhs).map { resultA, resultB in
    zip(resultA, resultB)
  }
}
```
- `Parallel`과 `Result`가 모두 `zip`을 제공하기 때문에, general하게 구현 가능.
- 하지만 `flatMap`은 불가능. Type specific한 구현이 반드시 필요.
- `map`을 `flatMap`으로 구현할 수 있는가. 가능.
- `zip`을 `flatMap`으로 구현할 수 있는가. 타입에 따라 불가능.
- What's the point? 정말로 포인트가 뭔지 모르겠다. flatMap이 이렇게나 파워풀하면서도 한계가 있다는 걸 알면 무엇이 나아지는가.

# [Episode #47 Predictable Randomness: Part 1](https://www.pointfree.co/episodes/ep47-predictable-randomness-part-1)
- Make the untestable testable.
-  Protocols witnesses에서 사용했던, Conditional conformance를 이용한 static extension 응용.
- Int뿐 아니라 Int32, UInt64 등을 함께 재공하려면 FixedWidthInteger protocol, Double의 경우에는 BinaryFloatingPoint protocol이 있음.
- Swift standard library의 `random(in:using:)`은 RandomNumberGenerator를 파라미터로 받을 수 있으며, 이를 통해 결과값을 통제할 수 있음.

# [Episode #48 Predictable Randomness: Part 2](https://www.pointfree.co/episodes/ep48-predictable-randomness-part-2)
- Gen을 testable하게 만들기 위해
  - Global `AnyRnadomNumberGenerator`를 생성하는 방법.
  ```Swift
  extension Gen where A: FixedWidthInteger {
    static func int(in range: ClosedRange<A>, using rng: inout RandomNumberGenerator) -> Gen {
      return Gen { .random(in: range, using: &rng) }
    }
  }
  ```
  - `RandomNumberGenerator`를 `AnyRandomNumberGenerator`로 감싸서 `run` function의 parameter로 받는 방법. `rng`가 mutable한 부분이 인터페이스에 표시되지 않는 문제점.
  ```Swift
  extension Gen where A: FixedWidthInteger {
    static func int(in range: ClosedRange<A>) -> Gen {
      let rng = SystemRandomNumberGenerator()
      return int(in: range, using: rng)
    }

    static func int<RNG: RandomNumberGenerator>(in range: ClosedRange<A>, using rng: RNG = SystemRandomNumberGenerator()) -> Gen {
      return Gen {
        var rng = rng 
        return .random(in: range, using: &rng)
      }
    }
  }
  ```
- `Array`를 위한 `Gen`을 생성해보면 nested problem이 다시 나타나는데, `flatMap`을 이용해 해결.
```Swift
extension Gen {
  func array(of count: Gen<Int>) -> Gen<[A]> {
    return count.flatMap { count in
      Gen<[A]> { rng in
        var array: [A] = []
        for _ in 1...count {
          array.append(self.run(&rng))
        }
        return array
      }
    }
  }
}
```
- Swift가 제공하는 randomness API들을 보다 composable하게 바꾸기 위해 소개한 `Gen`. 여기에 이제 testablility를 부여하기 위한 여정.
- ...인데 거듭 읽어도 잘 못따라가고 있음. 제시한 문제들을 잘 풀어낸 건 이해했는데, 이걸 어떻게 응용해야하는가.

# [Episode #49 Generative Art: Part 1](https://www.pointfree.co/episodes/ep49-generative-art-part-1)
- Bump function을 만들어내고, 그 parameter들을 `Gen<CGFloat>`를 통해서 구현해내는 과정
- ...인데 반포기 상태. 그저 마법 같은...
```Swift
func bump(
  amplitude: CGFloat,
  center: CGFloat,
  plateauSize: CGFloat,
  curveSize: CGFloat
  ) -> (CGFloat) -> CGFloat {
  return { x in
    let plateauSize = plateauSize / 2
    let curveSize = curveSize / 2
    let size = plateauSize + curveSize
    let x = x - center
    return amplitude * (1 - g((x * x - plateauSize * plateauSize) / (size * size - plateauSize * plateauSize)))
  }
}
 
let curve = zip4(with: bump(amplitude:center:plateauSize:curveSize:))(
  Gen<CGFloat>.float(in: -20...(-1)),
  Gen<CGFloat>.float(in: -60...60)
    .map { $0 + canvas.width / 2 },
  Gen<CGFloat>.float(in: 0...60),
  Gen<CGFloat>.float(in: 10...60)
)
```

# [Episode #50 Generative Art: Part 2](https://www.pointfree.co/episodes/ep50-generative-art-part-2)
- `Gen` 조합을 통해 라인 별 bump 갯수도 randomize. 
- 테스트에서 randomness를 통제하기 위해, Environment를 통해 rng 주입.
- Singleton을 사용하던 코드들을 모두 `Current`로 교체.
```Swift
struct Environment {
  var calendar = Calendar.current
  var date = { Date() }
  var rng = AnyRandomNumberGenerator(rng: SystemRandomNumberGenerator())
}

var Current = Environment()

extension Environment {
  static let mock = Environment(
    calendar: Calendar(identifier: .gregorian),
    date: { Date.init(timeIntervalSince1970: 1234567890) },
    rng: AnyRandomNumberGenerator(rng: LCRNG(seed: 1))
  )
}
```
- `rng`를 주입함으로써, 테스트에서는 deterministic한 결과를 얻어냄.
- `Environment`를 통한 주입이 너무 좋아서, 만나는 프로젝트마다 모두 사용한다고 하는데... 모듈화된 구조에서는 어떻게 할지 궁금하다.

# [Episode #51 Structs 🤝 Enums](https://www.pointfree.co/episodes/ep51-structs-enums)
- People sometimes call structs `product` types.
- People sometimes call enums `sum` types.
- struct의 member wise initializer와 유사하게, enum도 일종의 생성자에 해당하는 함수를 갖는다.
```Swift
Either<Int, String>.left
// (Int) -> Either<Int, String>
Either<Int, String>.right
// (String) -> Either<Int, String>
```
- Algebra에서는 product와 sum이 똑같이 중요하다. 하지만 Swift에서는 struct를 enum보다 중요하게 여기는 듯 하다. 
- Anonymous struct로 tuple을 지원하지만, anonymous enum이란 건 없다.
- 상상해본다면 이런 모습일 것...
```Swift
let _: (Int | String)
func render(data: (user: [User] | empty | loading)) {
  switch data {

  }
}
```

# [Episode #52 Enum Properties](https://www.pointfree.co/episodes/ep52-enum-properties)
- `왜 우리 enum이 기를 죽이고 그래요.`
- enum의 associated value를 얻어내는 방식은 struct에 비해 너무 불편하다.
- Swift5에 추가된 Result.get() 함수.
```Swift
enum Validated<Valid, Invalid> {
  case valid(Valid)
  case invalid([Invalid])
  
  var valid: Valid? {
    guard case let .valid(value) = self else { return nil }
    return value
  }

  var invalid: [Invalid]? {
    guard case let .invalid(value) = self else { return nil }
    return value
  }
}
```
- 하지만 enum마다 일일이 직접 정의해줘야하는 것은 그대로. 
- 그러니까 다음 episode에서 code generation에 대해 알아보자!

# [Episode #53 Swift Syntax Enum Properties](https://www.pointfree.co/episodes/ep53-swift-syntax-enum-properties)
- [SwiftSyntax](https://github.com/apple/swift-syntax)로 enum properties를 만들어내자.
- `SyntaxVisitor` class를 상속하여, 각 요소를 방문할 때마다 호출될 코드를 작성할 수 있음.
- 하지만 우리가 원하는 건, 이 code generation이 build system의 일부로서 동작하는 것.

# [Episode #54 Advanced Swift Syntax Enum Properties](https://www.pointfree.co/episodes/ep54-advanced-swift-syntax-enum-properties)
- SwiftSyntax를 이용해 associatedValue가 여럿이거나 없는 경우도 처리.
- 다시 정리해보면... Swift가 struct에 기본 제공하는 인터페이스들을 enum에게 똑같이 제공하겠다.
- Visitor 구현은 다 했지만, 아직 배포 가능한 라이브러리도 아니고, testable하지도 않다.

# [Episode #55 Swift Syntax Command Line Tool](https://www.pointfree.co/episodes/ep55-swift-syntax-command-line-tool)
- print()의 output parameter를 이용하면, 작은 수정으로도 기존 구현을 테스트할 수 있지만.
```Swift
public func print<Target>(_ items: Any..., separator: String = " ", terminator: String = "
", to output: inout Target) where Target : TextOutputStream
```
- XCTAssertEqual()로 큰 데이터를 비교할 때, 정작 어디 부분이 다른지 파악하기 어렵다.
- https://github.com/pointfreeco/swift-snapshot-testing
- Code generation library들은 생성된 코드가 Swift 문법 상으로도 문제가 없는지 확인할 필요가 있다.
- 마지막으로 Swift Package Manager의 excutable target을 이용해 command line tool 제공.

# [Episode #56 What Is a Parser?: Part 1](https://www.pointfree.co/episodes/ep56-what-is-a-parser-part-1)
- 각 타입이 제공하는 parsing intializer. 이 경우, custom format은 사용할 수 없다. 
- NSRegularExpress, Scanner. 조합이 불가능하고, 너무 오래되었다. Generic 같은 것조차 지원하지 않음.
- latitude/longitude parser를 전통적인 방법으로 일단 만들어봄.
```Swift
// 40.6782° N, 73.9442° W
```
- Edge case 처리를 위한 로직은 계속 불어나고, 구현된 코드 중에 어느 것도 외부에서 reuse할 수 없다.

# [Episode #57 What Is a Parser?: Part 2](https://www.pointfree.co/episodes/ep57-what-is-a-parser-part-2)
- Parser란 무엇인가. 결국 String을 받아서 특정 타입을 리턴하는 것.
- Deep-link를 route enum으로 바꾼다거나, command line tool의 argument를 enum으로 바꾸기도.
```Swift
struct Parser<A> {
  let run: (String) -> A?
}
```
- 우리가 원하는 건 multi-step process. 
- String의 일부를 parsing하고 나머지를 다음 parser에게 넘겨주는 방식.
```Swift
struct Parser<A> {
  let run: (String) -> (match: A?, rest: String)
}
let int = Parser<Int> { str in
  let prefix = str.prefix(while: { $0.isNumber })
  guard let int = Int(prefix) else { return (nil, str) }
  let rest = String(str[prefix.endIndex...])
  return (int, rest)
}
```
- 그리고 Substring의 신묘한 힘. startIndex를 사용함으로써 문자열 복사를 피할 수도 있다.
```Swift
struct Parser<A> {
  let run: (inout Substring) -> A?
}
```

# [Episode #58 What Is a Parser?: Part 3](https://www.pointfree.co/episodes/ep58-what-is-a-parser-part-3)
- Extensible and composable parsers.
- Episode #56의 latitude/longitude parser를 다시 구현해보면
```Swift
let northSouth = Parser<Double> { str in
  guard
    let cardinal = str.first,
    cardinal == "N" || cardinal == "S"
    else { return nil }
  str.removeFirst(1)
  return cardinal == "N" ? 1 : -1
}
let eastWest = Parser<Double> { str in
  guard
    let cardinal = str.first,
    cardinal == "E" || cardinal == "W"
    else { return nil }
  str.removeFirst(1)
  return cardinal == "E" ? 1 : -1
}
func parseLatLong(_ coordString: String) -> Coordinate? {
  var str = coordString[...]
  guard
    let lat = double.run(&str),
    literal("° ").run(&str) != nil,
    let latSign = northSouth.run(&str),
    literal(", ").run(&str) != nil,
    let long = double.run(&str),
    literal("° ").run(&str) != nil,
    let longSign = eastWest.run(&str)
    else { return nil }
  return Coordinate(latitude: lat * latSign, longitude: long * longSign)
}
```
- 감탄. 또 감탄.

# [Episode #59 Composable Parsing: Map](https://www.pointfree.co/episodes/ep59-composable-parsing-map)
- Parser의 map을 구현하면.
```Swift
extension Parser {
  func map<B>(_ f: @escaping (A)-> B) -> Parser<B> {
    return Parser<B> { str in
      self.run(&str).map(f)
    }
  }
}
```
- NorthSouth를 map으로 구현해보면.
```Swift
let northSouth = char
  .map {
    $0 == "N" ? Optional(1.0)
      : $0 == "S" ? -1
      : nil
}
```
- 이렇게 되면 Parser<Double?>이 되어버린다. 
- N도 S도 아닌 경우 consume을 아예 하지 말아야 하기 때문에, value 대신 Parser를 return 하는 것을 생각해볼 수 있다.
```Swift
let northSouth = char
  .map {
    $0 == "N" ? always(1.0)
      : $0 == "S" ? always(-1)
      : .never
}
```
- Parser<Parser<Double>>을 리턴하게 되기 때문에, flatMap이 필요해지게 된다.

# [Episode #60 Composable Parsing: Flat‑Map](https://www.pointfree.co/episodes/ep60-composable-parsing-flat-map)
- Parser type의 flatMap
```Swift
// flatMap: ((A) -> M<B>) -> (M<A>) -> M<B>
extension Parser {
  func flatMap<B>(_ f: @escaping (A) -> Parser<B>) -> Parser<B> {
    return Parser<B> { str in
      let original = str
      let matchA = self.run(&str)
      let parserB = matchA.map(f)
      guard let matchB = parserB?.run(&str) else {
        str = original
        return nil
      }
      return matchB
    }
  }
}
let coord = double
  .flatMap { lat in
    literal("° ")
      .flatMap { _ in
        northSouth
          .flatMap { latSign in
            literal(", ")
              .flatMap { _ in
                double
                  .flatMap { long in
                    literal("° ")
                      .flatMap { _ in
                        eastWest
                          .map { longSign in
                            Coordinate(
                              latitude: lat * latSign,
                              longitude: long * longSign
                            )
                          }
                      }
                  }
              }
          }
    }
}
// Parser<Coordinate>
```
- flatMap이 callback hell을 해결해준다고 했는데 어째서 이런 코드가 나왔는가.
- 살펴보면... 이전 parser의 결과를 다음 parser가 사용하고 있지 않고, 마지막에 Coordinate 생성 시에 한꺼번에 사용됨.

# [Episode #61 Composable Parsing: Zip](https://www.pointfree.co/episodes/ep61-composable-parsing-zip)
- Parser type의 flatMap
```Swift
// zip: (F<A>, F<B>) -> F<(A, B)>
func zip<A, B>(_ a: Parser<A>, _ b: Parser<B>) -> Parser<(A, B)> {
  return Parser<(A, B)> { str in
    let original = str
    guard let matchA = a.run(&str) else { return nil }
    guard let matchB = b.run(&str) else {
      str = original
      return nil
    }
    return (matchA, matchB)
  }
}
let latitude = zip(double, literal("° "), northSouth)
  .map { lat, _, latSign in lat * latSign }
// Parser<Double>
let longitude = zip(double, literal("° "), eastWest)
  .map { long, _, longSign in long * longSign }
// Parser<Double>
let coord2 = zip(latitude, literal(", "), longitude)
  .map { lat, _, long in
    Coordinate(
      latitude: lat,
      longitude: long
    )
}
// Parser<Coordinate>
```
- 그리고 Combine이나 Rx처럼... zip3, zip4, zip5...
- asking “what’s the point?” This is our chance to bring everything down to earth, and make sure we aren’t getting into the theoretical weeds too much. 재미있는 문구.
- Parser type을 정의하고 map, flatMap, zip을 제공함으로써 작은 parser들을 compose할 수 있게 되었다.

# [Episode #62 Parser Combinators: Part 1](https://www.pointfree.co/episodes/ep62-parser-combinators-part-1)
- 하지만 여전히 map, flatMap, zip 만으로 풀기 어려운 문제들이 있다.
- Skipping characters.
```Swift
coord.run("   40.446°    N,   79.982°   W   ")
// (nil, rest "   40.446°    N,   79.982°   W   ")
```
- White space를 skip하는 parser를 추가로 만들면 된다.
```Swift
let oneOrMoreSpaces = Parser<Void> { str in
  let prefix = str.prefix(while: { $0 == " " })
  guard !prefix.isEmpty else { return nil }
  str.removeFirst(prefix.count)
  return ()
}
let zeroOrMoreSpaces = Parser<Void> { str in
  let prefix = str.prefix(while: { $0 == " " })
  str.removeFirst(prefix.count)
  return ()
}
```
- 그리고 더 일반화된 방법은...
```Swift
func prefix(while p: @escaping (Character) -> Bool) -> Parser<Substring> {
  return Parser<Substring> { str in
    let prefix = str.prefix(while: p)
    str.removeFirst(prefix.count)
    return prefix
  }
}
let zeroOrMoreSpaces = prefix(while: { $0 == " " })
  .map { _ in () }
let oneOrMoreSpaces = prefix(while: { $0 == " " })
  .flatMap { $0.isEmpty ? .never : always(()) }
```
- 이처럼 Parser를 input 또는 output으로 다루는 함수들을 parser combinators 라고 부른다. `prefix`뿐 아니라 `map`, `zip`, `flatMap`, `literal` 등이 모두 포함.

# [Episode #63 Parser Combinators: Part 2](https://www.pointfree.co/episodes/ep63-parser-combinators-part-2)
- Parsing multiple values. `map`, `flatMap`, `zip`만으로는 해결하기 어려움.
```Swift
func zeroOrMore<A>(_ p: Parser<A>, separatedBy s: Parser<Void>) -> Parser<[A]> {
  return Parser<[A]> { str in
    var original = str
    var matches: [A] = []
    while let match = p.run(&str) {
      original = str
      matches.append(match)
      if s.run(&str) == nil else {
        return matches
      }
    }
    str = original
    return matches
  }
}
let commaOrNewline = char
  .flatMap { $0 == "," || $0 == "\n" ? always(()) : .never })
// Parser<Void>
zeroOrMore(money, separatedBy: commaOrNewline)
// Parser<[Money]>
```
- 이들을 higher-order parser라고.

# [Episode #64 Parser Combinators: Part 3](https://www.pointfree.co/episodes/ep64-parser-combinators-part-3)
- 복수의 parser 중 하나라도 parsing에 성공할 경우.
```Swift
func oneOf<A>(_ ps: [Parser<A>]) -> Parser<A> {
  return Parser<A> { str in
    for p in ps {
      if let match = p.run(&str) {
        return match
      }
    }
    return nil
  }
}
```
- 지금까지 소개된 parser combinators. `oneOf`, `zeroOrMore`, `prefix`
- New line parsing `separatedBy`에 `literal("\n")`으로 간단히 처리.
- 알고리즘 문제 풀 때, input parsing에 한 번 사용해볼 것.

# [Episode #65 SwiftUI and State Management: Part 1](https://www.pointfree.co/episodes/ep65-swiftui-and-state-management-part-1)
- 드디어 SwiftUI라니...
- 문제점들. Make applications more understandable and testable, such as pushing side effects to the boundaries, using pure functions as much as possible, and putting an emphasis on functions and composition above all other abstractions.
- `@State`는 멋지지만, 다른 view로 변경사항을 전파하려면 추가적인 작업 필요하다.
- `@State`로 정의된 property를 child view에 `@ObjectBinding`로 전달.

# [Episode #66 SwiftUI and State Management: Part 2](https://www.pointfree.co/episodes/ep66-swiftui-and-state-management-part-2)
- 별다른 내용 없이 SwiftUI 이야기 계속.
- $을 통해서 주입한 값은 내부에서 변경도 가동. `alert()`, `sheet()` 등.

# [Episode #67 SwiftUI and State Management: Part 3](https://www.pointfree.co/episodes/ep67-swiftui-and-state-management-part-3)
- SwiftUI의 좋은 점.
  - 선언적 UI. `body`에서만 View를 기술할 수 있다는 점.
  - Local state를 위한 `@State`.
  - Multiple screens를 위한 `@ObjectBinding`.
  - 무엇보다도 구조에 대한 strong opinion.
- SwiftUI에 더 요구되는 점.
  - Apple has yet to give us a solution for truly modeling our global app state in a scaleable way. 하지만 `@Published`가 추가되면서 나아졌다.
  - State mutation 코드가 view 구현부 내에 여기저기 흩어져있다.
  - alert()에서 사용되는 것처럼 2-way binding도 마찬가지. hidden mutation.
  - 지금으로서는 mutation을 둘 곳에 대한 team guideline 같은 걸로 해결해야 한다.
  - Side effect를 다루는 방식에 대해서도 Apple은 특별한 가이드를 주지 않았다.
  - State management isn't composable. 하지만 `@Binding`이 추가되면서 sub-state를 read-only로 표현할 수 있게 되었다.
  - SwiftUI isn't testable.

# [Episode #68 Composable State Management: Reducers](https://www.pointfree.co/episodes/ep68-composable-state-management-reducers)
- `class`로 선언된 global state. `@Published`를 property 마다 써줘야하는 부분도 개선 포인트.
- State를 `struct`로 변경하고, `ObservableObject`로 wrapping하기.
- State 자체는 SwiftUI나 Combine에 의존하지 않게 된 것도 장점.
```Swift
final class Store<Value>: ObservableObject {
  @Published var value: Value
  init(initialValue: Value) {
    self.value = value
  }
}
```
- State mutation 구문이 View에 여기저기 퍼져 있는 문제.
- State에 reducer를 구현함으로써 처리. 다만 `AppReducer`가 모든 스크린의 mutation을 담당할 수는 없는 일.

# [Episode #69 Composable State Management: State Pullbacks](https://www.pointfree.co/episodes/ep69-composable-state-management-state-pullbacks)
- `appReducer`를 어떻게 더 나누고 조합할 것인가.
```Swift 
func combine<Value, Action>(
  _ reducers: (inout Value, Action) -> Void...
) -> (inout Value, Action) -> Void {
  return { value, action in
    for reducer in reducers {
      reducer(&value, action)
    }
  }
}
let appReducer = combine(
  counterReducer,
  primeModalReducer,
  favoritePrimesReducer
)
```
- 하지만 여전히 reducer들이 불필요하게 global state를 다 알아야한다.
- Pullback을 대체 뭐라고 이해해야할까.
```Swift
func pullback<LocalValue, GlobalValue, Action>(
  _ reducer: @escaping (inout LocalValue, Action) -> Void,
  value: WritableKeyPath<GlobalValue, LocalValue>
) -> (inout GlobalValue, Action) -> Void {
  return { globalValue, action in
    reducer(&globalValue[keyPath: value], action)
  }
}
let appReducer = combine(
  pullback(counterReducer, value: \.count),
  primeModalReducer,
  pullback(favoritePrimesReducer, value: \.favoritePrimesState)
)
```

# [Episode #70 Composable State Management: Action Pullbacks](https://www.pointfree.co/episodes/ep70-composable-state-management-action-pullbacks)
- AppAction은 여전히 global action이다. State 처럼 pullback을 구현할 수 있을까.
- enum property를 구현한다면 key path를 이용한 동일한 접근이 가능해진다.
```Swift
func pullback<GlobalValue, LocalValue, GlobalAction, LocalAction>(
  _ reducer: @escaping (inout LocalValue, LocalAction) -> Void,
  value: WritableKeyPath<GlobalValue, LocalValue>,
  action: WritableKeyPath<GlobalAction, LocalAction?>
) -> (inout GlobalValue, GlobalAction) -> Void {
  return { globalValue, globalAction in
    guard let localAction = globalAction[keyPath: action] else { return }
    reducer(&globalValue[keyPath: value], localAction)
  }
}
let _appReducer: (inout AppState, AppAction) -> Void = combine(
  pullback(counterReducer, value: \.count, action: \.counter),
  pullback(primeModalReducer, value: \.self, action: \.primeModal),
  pullback(favoritePrimesReducer, value: \.favoritePrimesState, action: \.favoritePrimes)
)
let appReducer = pullback(_appReducer, value: \.self, action: \.self)
```
- `higher-order ***`란 무엇인가. `Gen`을 input으로 받아 `Gen`을 리턴하고. Reducer를 input으로 받아 reducer를 리턴하는.

# [Episode #71 Composable State Management: Higher-Order Reducers](https://www.pointfree.co/episodes/ep71-composable-state-management-higher-order-reducers)
- 그것은 바로 higher-order constructions.
- `combine`과 `pullback`이 higher-order reducer.
```Swift
func activityFeed(
  _ reducer: @escaping (inout AppState, AppAction) -> Void
) -> (inout AppState, AppAction) -> Void {

  return { state, action in
    switch action {
    case .counter:
      break

    case .primeModal(.removeFavoritePrimeTapped):
      value.activityFeed.append(
        .init(timestamp: Date(), type: .removedFavoritePrime(value.count))
      )

    case .primeModal(.addFavoritePrime):
      value.activityFeed.append(
        .init(timestamp: Date(), type: .saveFavoritePrimeTapped(value.count))
      )

    case let .favoritePrimes(.deleteFavoritePrimes(indexSet)):
      for index in indexSet {
        value.activityFeed.append(
          .init(timestamp: Date(), type: .removedFavoritePrime(value.favoritePrimes[index]))
        )
      }
    }

    reducer(&state, action)
  }
}

ContentView(
  store: Store(
    initialValue: AppState(),
    reducer: activityFeed(appReducer)
  )
)
```
- 그리고 모든 action에 logging을 달고 싶다면...
```Swift
func logging<Value, Action>(
  _ reducer: @escaping (inout Value, Action) -> Void
) -> (inout Value, Action) -> Void {
  return { value, action in
    reducer(&value, action)
    print("Action: \(action)")
    print("State:")
    dump(value)
    print("---")
  }
}
ContentView(
  store: Store(
    value: AppState(),
    reducer: with(
      appReducer,
      compose( // import Overture
        logger,
        activityFeed
      )
    )
  )
)
```
- View styling, randomness, snapshot testing, parsing 같은 아이디어를 반복해서 구현 중.
- Atomic primitive를 구현하고 그를 조합해서 복잡한 기능을 구현해내는 것.
- 이 시리즈를 시작할 때 지목한 5개의 문제점 중 2개를 풀어보았다. (그리고... Modular architecture에서 어떻게 할 것인지. Side effects를 어떻게 다룰 것인지. 어떻게 Testable하게 만들 것인지.)

# [Episode #72 Modular State Management: Reducers](https://www.pointfree.co/episodes/ep72-modular-state-management-reducers)
- 5개의 문제점. 원문 그대로.
  - Create complex app state models, ideally using simple value types.
  - Have a consistent way to mutate the app state instead of just littering our views with mutation code.
  - Have a way to break a large application down into small pieces that can be glued back together to form the whole.
  - Have a well-defined mechanism for executing side effects and feeding the results back into our application.
  - Have a story for testing our application with minimal setup and effort.
- Modularity란 무엇인가. 단절시키는 것. 필요한 것만 사용하는 것.
- `primeModalReducer`는 AppState를 참조하고 있기 때문에, 모듈로 이동시키기만 해서는 안된다.
```Swift
extension AppState {
  var primeModal: PrimeModalState {
    get {
      PrimeModalState(
        count: self.count,
        favoritePrimes: self.favoritePrimes
      )
    }
    set {
      self.count = newValue.count
      self.favoritePrimes = newValue.favoritePrimes
    }
  }
  ...
}
let appReducer = combine(
  ...
  pullback(primeModalReducer, value: \.primeModal, action: \.primeModal),
  ...
}
```

# [Episode #73 Modular State Management: View State](https://www.pointfree.co/episodes/ep73-modular-state-management-view-state)
- AppState도 AppAction도 분리했지만, View는 아직 그러지 못했다.
- 코드는 이해했는데, 설명이 잘 이해되지 않는 에피소드;;;
```Swift
public final class Store<Value, Action>: ObservableObject {
  private let reducer: (inout Value, Action) -> Void
  @Published public private(set) var value: Value
  private var cancellable: Cancellable?

  public init(initialValue: Value, reducer: @escaping (inout Value, Action) -> Void) {
    self.reducer = reducer
    self.value = initialValue
  }

  public func send(_ action: Action) {
    self.reducer(&self.value, action)
  }

  // ((Value) -> LocalValue) -> ((Store<Value, _>) -> Store<LocalValue, _>
  // ((A) -> B) -> ((Store<A, _>) -> Store<B, _>
  // map: ((A) -> B) -> ((F<A>) -> F<B>

  func view<LocalValue>(
    _ f: @escaping (Value) -> LocalValue
  ) -> Store<LocalValue, Action> {
    let localStore = Store<LocalValue, Action>(
      initialValue: f(self.value),
      reducer: { localValue, action in
        self.send(action)
        localValue = f(self.value)
    }
    )
    localStore.cancellable = self.$value.sink { [weak localStore] newValue in
      localStore?.value = f(newValue)
    }
    return localStore
  }
}
IsPrimeModalView(
  store: self.store.view { ($0.count, $0.favoritePrimes) }
)
```
- 여튼! LocalStore의 mutation이 GlobalStore로 전달되고, 다시 다른 LocalStore로 퍼져나가는.

# [Episode #74 Modular State Management: View Actions](https://www.pointfree.co/episodes/ep74-modular-state-management-view-actions)
- Store.view()의 Action을 LocalAction으로 바꿀 수 있으면, View를 아예 모듈로 보내는 것이 가능해짐.
- Action에 대해서도 LocalValue와 동일한 접근이 가능하고... 둘을 한꺼번에 하는 것도 자연스럽다.
```Swift
public func view<LocalValue, LocalAction>(
  value toLocalValue: @escaping (Value) -> LocalValue,
  action toGlobalAction: @escaping (LocalAction) -> Action
) -> Store<LocalValue, LocalAction> {
  let localStore = Store<LocalValue, LocalAction>(
    initialValue: toLocalValue(self.value)
    reducer: { localValue, localAction in
      self.send(toGlobalAction(localAction))
      localValue = toLocalValue(self.value)
  }
  )
  localStore.cancellable = self.$value.sink { [weak localStore] newValue in
    localStore?.value = toLocalValue(newValue)
  }
  return localStore
}
```
- 여기까지 되고 나면 각 View는 LocalValue와 LocalAction만 사용하기 때문에, 모듈화가 가능해진다.

# [Episode #75 Modular State Management: The Point](https://www.pointfree.co/episodes/ep75-modular-state-management-the-point)
- Module이 제대로 분리되어 있다면 Playground를 이용해 각 모듈의 View를 앱 실행 없이 확인할 수 있다.
```Swift
import ComposableArchitecture
import FavoritePrimes
import SwiftUI
import PlaygroundSupport
PlaygroundPage.current.liveView = UIHostingController(
  rootView: FavoritePrimesView(
    store: Store<[Int], FavoritePrimesAction>(
      initialValue: [2, 3, 5, 7, 11],
      reducer: favoritePrimesReducer
    )
  )
)
```
- CounterView의 경우, 대응하는 reducer가 아직 없기 때문에...
- 코드를 좀 더 살펴볼 것. 
- https://github.com/pointfreeco/episode-code-samples/tree/main/0075-modular-state-management-wtp
- Exercise가 흥미로움. 우리는 모든 mutation이 store와 reducer를 통하길 바라지만, 2-way binding은 그에 적합하지 않음.

# [Episode #76 Effectful State Management: Synchronous Effects](https://www.pointfree.co/episodes/ep76-effectful-state-management-synchronous-effects)
- This means that side-effects are nothing more than hidden inputs or outputs of a function.
- 결국... argument로 전달받거나 return하지 않는 무언가를 변경한다면 그것이 side-effects.
- 함수 내의 print() 호출도 역시 side-effects.
- Side-effects란 무엇인가...
```Swift
public typealias Effect = () -> Void
```
- Side-effects를 유발하는 구현을 `Effect`타입으로 return하도록 바꾼다면...
- 그건 누가 호출할거지?

# [Episode #77 Effectful State Management: Unidirectional Effects](https://www.pointfree.co/episodes/ep77-effectful-state-management-unidirectional-effects)
- Push effects to the boundary of a function.
- Saving의 경우 실행하면 그만이지만(fire-and-forget operation), loading 같은 경우에는 결과를 이용해서 다음 작업을 해야한다.
- Synchronous한 경우 먼저 해결.
- Effect의 정의를 바꿔서, return된 Action을 caller가 send하도록 한다.
```Swift
public typealias Effect<Action> = () -> Action?
public typealias Reducer<Value, Action> = (inout Value, Action) -> Effect<Action>
```
- 무엇이 Unidirectional인가. Mutation을 하고 싶다면 Action을 정의한 후, store에서만 수행하도록 해야한다.

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

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

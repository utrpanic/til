## [1. Hello, Combine!](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/1-hello-combine)
- The first "modern-day" reactive solution. Reactive Extensions for .NET (2009)
- The 3 key moving pieces: publishers, operators, subscribers.

## [2. Publishers & Subscribers](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/2-publishers-subscribers)
- Publisher. 
- Subscriber.`sink(_:_:)`, `assign(to:on:)`
  - `assign(to:on:)` 와 `assign(to:)`의 차이. `assign(to:)`는 AnyCancellable을 return하지 않고, @Published property가 deinitialize될 때 cancel됨.
  - Demand는 증가시키는 것만 가능.
- Cancellable. Subscription의 return value를 저장하지 않으면, 해당 Cancellable이 생성된 scope을 벗어날 때 cancel됨.
- Subject: Non-Combine imperative code에서 Combine subscribers에게 value를 보낼 수 있게 해줌.
- Bridging Combine publishers to async/await 
```Swift
Task {
  for await element in subject.values {
    print("Element: \(element)")
  }
  print("Completed.")
}
```

## [3. Transforming Operators](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/3-transforming-operators)
- `collect()`: event들을 array로. count가 없으면 upstream이 끝나는 시점에, count가 있으면 해당 갯수가 채워질 때마다.
- `map(_:)`: Key path로 바로 mapping하는 것도 가능.
- `flatMap(maxPublishers:_:)`: Multiple upstream을 single downstream으로 바꿔줌.
- `replaceNil(with:)`: Upstream event가 nil인 경우, 지정한 값을 방출. Optional을 리턴하는 것은 안됨.
- `replaceEmpty(with:)`: Upstream이 event 방출 없이 종료될 경우, 지정한 값을 방출.
- `scan(_:_:)`: 이전에 방출한 값을 closure의 parameter로 다시 받을 수 있음. Reduce와 유사한 동작.
- `collect`나 `flatMap`처럼 buffer size를 파라미터로 받는 operator의 경우, 메모리 문제에 신경써야 함.

## [4. Filtering Operators](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/4-filtering-operators)
- `filter`, `removeDuplicates`, `compactMap`, `ignoreOutput`
- `first(where:)`, `last(where:)`: first는 lazy, last는 greedy.
- `dropFirst`, `drop(while:)`, `drop(untilOutputFrom:)`
- `prefix`, `prefix(while:)`, `prefix(untilOutputFrom:)`
- Throwable closure를 파라미터로 받는 경우, `try` prefix 사용. Error throw 시, completion.

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

## [5. Combining Operators](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/5-combining-operators)
- `prepend`, `append`
- `switchToLatest`: `Publisher`를 emit하는 `Publisher`의 경우.
- `merge(with:)`: 동일한 `Output` type을 가진 `Publisher`의 경우.
- `combineLatest`: 어느 `Publisher`든 새로운 value가 emit될 때마다 tuple을 emit.
- `zip`: 모든 `Publisher`가 새로운 value를 emit할 경우, 해당 tuple을 순서대로.

## [6. Time Manipulation Operators](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/6-time-manipulation-operators)
- `delay(for:tolerance:scheduler:options)`: Upstream의 event를 지정시간 후에 emit.
- `collect(_:options)`: `collect`의 overload.
- `debounce(for:scheduler:)`: Upstream에서 지정 시간 동안 새로운 event를 emit하지 않으면, 마지막 event를 downstream에 emit.
- `throttle(for:scheduler:latest:)`: Upstream에서 event를 emit하면 downstream에 emit 후, 지정시간 동안 emit된 event 중 첫 번째 혹은 마지막 event를 지정시간이 되면 emit. (책 내용과 코드 실행 결과가 다름.)
- `timeout(_:scheduler:customError:)`: Upstream에서 지정 시간 동안 새로운 value가 emit되지 않으면, complete.
- `measureInterval(using:)`: RunLoop 또는 DispatchQueue를 사용할 수 있음. Event 사이의 timeInterval을 value로 emit.

## [7. Sequence Operators](https://www.raywenderlich.com/books/combine-asynchronous-programming-with-swift/v3.0/chapters/7-sequence-operators)
- Publisher들은 실제로도 sequence. Swift standard library가 제공하는 sequence를 위한 인터페이스들이 유사하게 제공됨.
- `min`, `max`: 둘 다 greedy.
- `first`, `last`, `output(at:)`: last만 greedy. 나머지는 lazy.
- `count`: Upstream의 value가 아닌 갯수를 emit. 물론 greedy.
- `contains`: Upstream의 value가 조건을 충족하면 true를 emit. 충족하지 못한 채로 finish되면 false를 emit.
- `allSatisfy`: Upstream이 finish되었을 때, 모든 value가 조건을 충족하면 true를 emit. 충족하지 못하는 value를 받으면 바로 subscription을 cancel함.
- `reduce`: Swift standard library와 동일. Upstream이 finish되었을 때, 마지막에 reduce 동작. Upstream의 value 마다 accumulated value를 emit하는 `scan`과는 다름.

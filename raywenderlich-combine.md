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

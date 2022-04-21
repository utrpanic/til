# 1. [Why Modern Swift Concurrency?](https://www.raywenderlich.com/books/modern-concurrency-in-swift/v1.0/chapters/1-why-modern-swift-concurrency)
- Existing Concurrency Issues
  1. Thread explosion. 너무 많은 thread를 만들면 active thread 간의 switching 때문에 성능이 저하될 수 있다.
  2. Priority inversion. Low-priority task가 high-priority task에 의해 block 될 수 있다.
  3. Lack of execution hierarchy. 모든 task를 독립적으로 관리해야 했다.
- Key New Features
  1. A cooperative thread pool.
  2. `async`/`await` syntax.
  3. Structured concurrency.
  4. Context-aware code compilation.
- `String`이 `Error`를 conform해서 바로 throw 하는 것 너무 신기.
- `MainActor`를 이용해 main thread에서 돌도록 강제할 수는 있지만... 이런걸 쓰면 테스트 작성에 문제가 생기진 않을까?
- 실제로 동작하는 thread와 상관없이, task 간에 계층이 존재한다.
- 최상위 task를 cancel하면 하위 task로 전파된다. `task(_:)` view modifier 하위의 task들은 해당 view가 사라지면 연쇄적으로 cancel된다.

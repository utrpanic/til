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

# 2. [Getting Started With async/await](https://www.raywenderlich.com/books/modern-concurrency-in-swift/v1.0/chapters/2-getting-started-with-async-await)
- Pre-async/await asynchrony. 
  - `completion` 호출 또는 에러 처리를 모두 개발자가 보장해야하고. 
  - Memory management. Weak capture 처리도 필요.
- Swift splits up your code into logical units called partial tasks, or partials.
- Computed property도 `async` 키워드 사용 가능.
- 순차적으로 호출해야한다면 `await`로 충분하지만, 동시에 호출해도 되는 상황이라면...? 
- Structured concurrency. Await them all together. Sequence나 tuple로 grouping 가능.
```Swift
do {
  async let files = try model.availableFiles()
  async let status = try model.status()
  let (filesResult, statusResult) = try await (files, status)
  self.files = filesResult
  self.status = statusResult
} catch {
  lastErrorMessage = error.localizedDescription
}
```
- `Task` is a type that represents a top-level asynchronous task.
- `Task(priority:operation)`: Inherits defaults from the current synchronous context
- `Task.detached(priority:operation)`: Not inherits defaults from the current synchronous context
- 실행은 분명 task가 생성된 actor에서 되지만, resume은 어느 스레드에서도 실행될 수 있다. 그래서 `MainActor`가 필요해지는 것.
```
Remember, you learned that every use of await is a suspension point, and your code might resume on a different thread. The first piece of your code runs on the main thread because the task initially runs on the main actor. But after the first await, your code can execute on any thread.
```

# 3. [AsyncSequence & Intermediate Task](https://www.raywenderlich.com/books/modern-concurrency-in-swift/v1.0/chapters/3-asyncsequence-intermediate-task)
- `AsyncSequence`. Swift standard library의 `Sequence`와 유사.
- `while` 문에서 사용하려면 `AsyncIterator`를 만들어야 함.
- SwiftUI의 `Environment`처럼 자신과 child task에게 값을 bind할 수 있는 `@TaskLocal` property. 남용하면 코드 읽기가 어려워진다.
- `Combine`의 `values` property. 간단하게 `AsyncSequence`를 얻어낼 수 있다. `Future`의 경우는 `value`.

# 4. [Custom Asynchronous Sequences With AsyncStream](https://www.raywenderlich.com/books/modern-concurrency-in-swift/v1.0/chapters/4-custom-asynchronous-sequences-with-asyncstream)
- `AsyncStream`을 통해 간단하게 asynchronous sequence를 만들어낼 수 있다.
- `continuation.yield()`가 호출될 때마다 값을 방출. Sequence의 종료는 `continuation.finish()`를 호출해야한다.

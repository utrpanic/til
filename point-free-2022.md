## [#191 Concurrency's Present: Queues and Combine](https://www.pointfree.co/episodes/ep191-concurrency-s-present-queues-and-combine)
- OperationQueue. GCD만큼 많이 사용되지는 않지만. 여튼 Thread의 여러 문제점을 많이 해결. 스레드풀, 의존성, 우선순위, 취소 등.
- GCD는 다시 그 위에 얹어진 추상화 레이어.  Serial by default. `target:`에 다른 queue를 전달함으로써, context 전파 가능.
- `.sync`/`.async`는 호출 thread의 block 여부, `.serial`/`.concurrent`는 실행하는 thread의 attribute.
- Combine은 엄밀히 이야기하면 concurrency를 위한 도구는 아니지만, `Defered`/`Future`로 구현 가능. 
- 결국 `async`/`await` 방식이 코드도 간결하고 직관적이라는...

## [#192 Concurrency's Future: Tasks and Cooperation](https://www.pointfree.co/episodes/ep192-concurrency-s-future-tasks-and-cooperation)
- Swift concurrency. Deeply integrated with the language itself.
- Suspended tasks can be resumed on a different thread.
- Task cancellation is so deeply ingrained into the system.
- `@TaskLocal`. Task 내에서 새로운 Task가 시작될 경우, parent task의 값들을 capture. 멋지긴한데, 그냥 파라미터로 받아서 넘기는 게 더 명확한 것 아닌가...
- Task를 사용하면 cooperative thread pool를 사용. await는 non-blocking 이기도 하고.

## [#193 Concurrency's Future: Sendable and Actors](https://www.pointfree.co/episodes/ep193-concurrency-s-future-sendable-and-actors)
- `@Sendable` closure. 모든 parameter가 `Sendable`이어야 하고, capture도 `Sendable`만 가능. Explicit capture를 하면..?
- Async 동작이지만 코드 상으로는 top to bottom으로 읽을 수 있도록. `@escaping`도 불필요해질 수 있음.
- `actor`. Read도 write도 async.
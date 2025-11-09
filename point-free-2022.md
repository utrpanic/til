## [#191 Concurrency's Present: Queues and Combine](https://www.pointfree.co/episodes/ep191-concurrency-s-present-queues-and-combine)
- OperationQueue. GCD만큼 많이 사용되지는 않지만. 여튼 Thread의 여러 문제점을 많이 해결. 스레드풀, 의존성, 우선순위, 취소 등.
- GCD는 다시 그 위에 얹어진 추상화 레이어.  Serial by default. `target:`에 다른 queue를 전달함으로써, context 전파 가능.
- `.sync`/`.async`는 호출 thread의 block 여부, `.serial`/`.concurrent`는 실행하는 thread의 attribute.
- Combine은 엄밀히 이야기하면 concurrency를 위한 도구는 아니지만, `Defered`/`Future`로 구현 가능. 
- 결국 `async`/`await` 방식이 코드도 간결하고 직관적이라는...


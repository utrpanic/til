# [Episode #138 Better Test Dependencies: Exhaustivity](https://www.pointfree.co/collections/dependencies/better-test-dependencies/ep138-better-test-dependencies-exhaustivity)
- Exhaustivity. (하나도 빠뜨리는 것 없이) 철저함, 완전함.
- Environment를 통해 dependency를 주입하지만, 특정 테스트 시나리오에서는 실제로 사용되지 않는 경우가 있다. `fatalError()`로 구현부를 채웠는데도 테스트가 성공한다면 더 확실하게 검증할 수 있다.
```Swift
extension UUID {
  static let unimplemented: () -> UUID = { fatalError() }
}

import Combine

extension Scheduler {
  static var unimplemented: AnySchedulerOf<Self> {
    Self(
      minimumTolerance: { fatalError() },
      now: { fatalError() },
      scheduleImmediately: { _, _ in fatalError() },
      delayed: { _, _, _, _ in fatalError() },
      interval: { _, _, _, _, _ in fatalError() }
    )
  }
}
```
- 해당 테스트 시나리오가 필요로 하는 dependency만 명시하는 것은, 테스트의 복잡도를 한 눈에 볼 수 있게 해준다.
- 또한, unimplemented dependency를 기본값으로 해두면, 새로운 dependency를 추가할 때에도 좋다.
- Adding analytics to features. 보통은 특정 라이브러리의 싱글톤 객체를 곳곳에서 호출. 거의 테스트 되지도 않으며, 어떤 이벤트들이 너무 많이 혹은 너무 적게 수집되고 있는지 알아채는 것도 어렵다.
```Swift
struct AnalyticsClient {
  var track: (String, [String: String]) -> Effect<Never, Never>
}
```
- 동일하게 `Environment`에 주입하고, `AnalyticsClient.unimplemented`도 구현.

# [Episode #139 Better Test Dependencies: Failability](https://www.pointfree.co/collections/dependencies/better-test-dependencies/ep139-better-test-dependencies-failability)
- `fatalError()`로 `.unimplemented`를 구현하니, 주입을 빠뜨린 경우 테스트 전체가 중단된다.
- Exhaustivity라는 건... 테스트에 사용되는 의존성만 정확하게 주입하는 걸 말하는 걸까?

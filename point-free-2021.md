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

# [Episode #140 Better Test Dependencies: Immediacy](https://www.pointfree.co/collections/dependencies/better-test-dependencies/ep140-better-test-dependencies-immediacy)
- `combine-schedulers`를 이용해 async 동작을 제어하려고 한다.
- https://github.com/pointfreeco/combine-schedulers
- `Scheduler`는 associated type을 갖기 때문에, 한번 사용하기 시작하면 generic 선언이 연쇄적으로 필요. Type erase를 이용하자!
- `delay`, `debounce`, `timeout` 같은 것들은 어떻게 테스트할 수 있는가. `ImmediateScheduler`를 사용하면 즉시 실행하는데다... 명시적인 `Scheduler.advance` 호출도 생략할 수 있다.

# [Episode #141 Better Test Dependencies: The Point](https://www.pointfree.co/collections/dependencies/better-test-dependencies/ep141-better-test-dependencies-the-point)
- Exhaustivity, Failability, Immediacy 모두 테스트를 위한 개선이었지만, 실제로 그 효과는 테스트에 국한되지 않는다.
- Immediate scheduler를 이용해 SwiftUI preview를 개선하고.
- (전반적으로 이해가 잘... 건너뛴 에피소드를 살펴보고 다시 보기.)

# [Episode #171 Modularization: Part 1](https://www.pointfree.co/episodes/ep171-modularization-part-1)
- 단일 앱을 SPM을 이용해 모듈화 하는 과정.
- Model이나 Helper 같이 외부 의존성이 적은 모듈을 먼저 추출.
- 그 후에 FeatureModule 모듈 추출.
- `It seems that Xcode and SPM pick up transitive dependencies.` 잘못된 디자인이 아닌가 계속 생각했는데, 이런 언급을 보니 transitive dependency란 개념이 원래 있는 건지도.

# [Episode #172 Modularization: Part 2](https://www.pointfree.co/episodes/ep172-modularization-part-2)
- Xcode preview의 단점으로...
  - Simulator에서는 되지만 preview에서는 안되는 것들이 많고.(slow animation, application lifecycle 등.)
  - 코드를 수정하면 preview가 바로 업데이트 되는 등, 오래 유지되지 않고.
  - Debugger를 연결할 수 없다.
- Horizontal vs. vertical modularity. 이미 우리가 해온 것들.

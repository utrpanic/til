# [Clean iOS Architecture pt 1: Analytics Architecture Overview](https://www.youtube.com/watch?v=PnqJiJVc0P8)
## The requirements
- It needs to be easy to log events from any view controller.
- The system should support any underlying system for actually sending events to some form of backend.
- The system should be highly testable and easy to verify.
- It should be easy to add, remove and modify events and get compile time errors whenever a call site needs to be updated.
## Implementation
- AnalyticsEngine protocol 및 구현체. AnalyticsEvent를 전송.
- AnalyticsManager가 존재. AnalyticsEngine을 주입받는 구조.
- ViewController들이 AnalyticsManager에 의존.
- AnalyticsEvent를 enum으로 구현하면 ViewController들이 모두 AnalyticsEvent에 의존하게 된다. switch가 필요한 경우가 아니라면 enum을 사용해선 안되며, struct로 구현한 후 각 ViewController가 필요로하는 event를 extension으로 처리하면 분리가 가능해짐.
- extension으로 처리할 경우, 이름 충돌 문제. struct extension 대신에 AnalyticsEvent를 protocol로 정의하는 방법도 가능.
- AnalyticsManager의 인터페이스와 AnalyticsEngine의 인터페이스에 차이가 없다면 Manager는 제거해도 되는 것.

# [Clean iOS Architecture pt.2: Good Architecture Traits](https://www.youtube.com/watch?v=C2GyNTN4j4o)
## What is architecture?
- MVC / MVP / MVVM / M** are design patterns.
- Architecture is about structuring the intent of your system.
- Soft system. Flexible / Maintainable / Scalable / 예측 가능하고 / Testable
## Improve Analytics Implementation
- ViewController가 log 타이밍이나 log 내용을 지정하는 상황을 해결해보자.
- 많은 사람들이 architectural decision을 느낌적인 느낌으로...
- 코딩하기 전에 설계를 하라. 오래 걸리지 않는다.

# [Clean iOS Architecture pt.3: Composing types in Swift](https://www.youtube.com/watch?v=GzFD7R_CI04)
- Presenter가 UseCaseOutput protocol을 conform.
- CrashlyticsLoginTracker도 UseCaseOutput protocol을 conform.
- FirebaseAnalyticsLoginTracker도... 와...
- UseCase가 셋 다 UserCaseOutput 타입으로 주입받고, 각각 타이밍에 맞춰서 호출.
- (그럼 UseCase는 누가 들고 있는 거지?)
- 그리고 UseCaseOutputComposer와 UseCaseFactory에 대한 테스트 작성. 아 진짜 근사하다.
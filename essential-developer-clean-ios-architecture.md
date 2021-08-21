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

# [Clean iOS Architecture pt.4: Clean Memory Management in Swift with WeakRef](https://www.youtube.com/watch?v=3XAdFuwHgew)
- weak property 없이 구현하는 법?
- Presenter가 output으로 ViewController를 갖고, UseCase가 output으로 Presenter를 가짐. 그리고 UseCase의 fetchData를 ViewController의 reloadData closure에 넣어줌. 결국 retain cycle.
- 그리고 실패하는 테스트 작성. 와 이런 것도 테스트로 작성할 수 있다니! 확인하고 싶은 객체를 weak으로 복사해놓고, tearDown에서 nil 체크.
- Memory Management 역시 responsibility. Presenter가 output을 weak으로 가져야한 다는 것을 알 필요는 없다. output에 ViewController 말고 다른 게 올 수도 있고.
- Presenter에 넣을 때 ViewController를 weak reference로 감싸서 넣자. 이거 그때 본 WeakObject 같은 것.
```Swift
final class WeakRef: WeatherDataPresenterOutput {

    weak var object: (AnyObject & WeatherDataPresenterOutput)?

    init(_ object: (AnyObject & WeatherDataPresenterOutput)) {
        self.object = object
    }

    func present(_ weather: WeatherViewModel) {
        object?.present(weather)
    }
}
```
- 그리고 다시 제네릭을 한숟갈 넣으면..
```Swift
final class WeakRef<T: AnyObject> {

    weak var object: T?

    init(_ object: T) {
        self.object = object
    }
}

extension WeakRef: WeatherDataPresenterOutput where T: WeatherDataPresenterOutput {

    func present(_ weather: WeatherViewModel) {
        object?.present(weather)
    }
}
```

# [Clean iOS Architecture pt.5: MVC, MVVM, and MVP (UI Design Patterns)](https://www.youtube.com/watch?v=qzTeyxIW_ow)
- MVC, MVVM, MVP 모두 아키텍쳐는 아니다. UI 디자인 패턴이다.
## MVC(1979)
- MVC도 View가 Model을 observing 하는 것은 동일.
- Controller와 View 모두 Model에 의존하고 있음. Model은 다른 모듈에 의존하지 않음.
- DB나 Network에 접근한다거나 하는 것은 어플리케이션의 몫이다. MVC에는 없는 개념이며, 이를 아키텍쳐가 아니라 UI 디자인 패턴이라고 부르는 이유.
## MVC(Apple)
- Controller가 Model과 View에 의존하고 있음. Controller가 Model과 View를 observing함. Notification이나 Callback, Delegate 모두 observing의 방법들.
- Model: Business Logic, Storage(DB), Networking, Parsing
- Controller: User Event Handling, Routing
- View: Formatting(Presentation), Rendering
- 개발자에 따라 Model과 Controller가 역할을 나눠갖는 방식에 조금 차이가  있지만, 여튼 누군가 너무 많은 역할을 한다는 뜻.
## MVVM
- Controller renamed ViewModel but...
- ViewModel은 Model에 의존할 뿐, View에는 의존하지 않는다.
- Binder가 차이점. View가 ViewModel의 변화에 맞춰 변경되도록.
- Model: Business Logic
- ViewModel: User Event Handling, Formatting(Presentation)
- View: Rendering
- Storage(DB), Networking, Parsing, Routing이 없는데... MVVM은 이를 해결하지 않았다.
- 이들을 결국 어딘가에 집어넣는 것이 개발자들이 해온 일.
## MVP
- Controller renamed Presenter
- Presenter는 ModelLayer의 인터페이스에 의존해서 ViewData를 만들어냄.
- Presenter는 ViewData를 View의 protocol을 통해 넘겨줌. View 구현체의 존재는 모른다.
- MVVM과 마찬가지로 Storage(DB), Networking, Parsing, Routing를 어디에서 구현해야하는가를 정의하지 않고 있다.
## with UIKit
- MVVM을 구현해도 UIViewController가 여전히 존재한다. Binder에 해당하는 기능이 없기 때문에, UIViewController가 View에 ViewModel을 binding한다. 그러다보니 MVC와 다를게 별로 없지 않은가. 결국 MVC in MVVM.
- MVP. 비슷한 상황. Presenter와 View 사이에 UIViewController. MVC embeded in MVP라고.

# [Clean iOS Architecture pt.6: VIPER – Design Pattern or Architecture?](https://www.youtube.com/watch?v=CkylrfKvf1A)
- https://www.objc.io/issues/13-architecture/viper/
- Architecture인가 Design Pattern인가. 각 Part들이 모든 요소들을 포함하지 못하고, 개발자들이 구현 시점에 이건 여기에 이건 저기에 둬야한다는 판단을 해야하는 게 여전히 문제다. More like architectural design pattern.
- Software Architecture is less about responsibilities and more about component communication.
- (언제 구현체가 아닌 프로토콜에 의존할 것인가. 1. 모듈/레이어가 다르다면 프로토콜에 의존. 2. 테스트가 불가능하면 프로토콜에 의존. 같은 모듈/레이어인데 테스트가 가능하다면 프로토콜에 의존할 이유가 없다. 해당 요소는 이미 테스팅되었으니까 의존하는 컴포넌트도 주입없이 테스트를 작성할 수 있지 않나?)
- VIPER. Good one. 하지만 아키텍쳐는 아니다. 

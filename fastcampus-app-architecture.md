# [슈퍼앱 운영을 위한 확장성 높은 앱 아키텍처 구축](https://fastcampus.co.kr/dev_red_rsj)

## 0. Intro

### 01. 모바일 개발자의 Scalability와 앱 아키텍쳐
- 앱의 유저가 많거나 적거나, 앱 개발을 할 때의 고민은 큰 차이가 없었던 것 같다.
- 그랩은 플랫폼당 200명 정도의 개발자들. PR이 하루만 넘어가도 main에서 100 commit 이상 뒤쳐지게 되는.
- 서비스의 성장을 따라갈 수 있는 확장 가능한 앱을 만드는 것.

## 1부. 코드 레벨 아키텍처. 재사용 가능한 코드를 만드는 스킬

### 01. 아키텍처와 Composition
- 객체를 조립해서 쓰는 방식을 Composition이라고 합니다.
- Favor object composition over class inheritance. - Gang of Four, Design Patterns(1994)
- map, flatmap을 이용한 compostion 실습.

### 02. 앱 비즈니스와 로직
- Redux Architecture. RIBs, VIPER 등

### 03. 복잡한 뷰 만들기
- 일단 RIBs의 동작 방식에 대한 설명.
- 화면 단위 이상으로 ViewController를 나눠서, ViewController가 너무 커지는 것을 방지하고, composite하게.
- View와 ViewController는 테스트 작성이 어렵기 때문에, 가능한 한 멍청하게 만든다.
- (결국 snapshot test로 보완할거라면, 약간은 View와 ViewController에게 부담을 줘도 되지 않을까.)
- (하지만 '약간'의 경계가 사람마다 다르니, 팀에서는 원칙을 좀 보수적으로 적용하는 것도 좋은 것 같다.)
- sink할 때마다 weak self를 쓰는 것이 불편하면, willResignActive에서 cancellables를 직접 cancel 해주는 것도 방법!!!

### 04. 복잡한 플로우 만들기 - 1
- 뷰에 얽매이지 않고 비즈니스 로직을 구현해봅시다.
- 하나의 riblet을 플로우에 따라 여러 곳에서 진입.
- (Parent가 child의 life cycle을 관리해야하는 것은 장점이 아니라, router를 통해 tree를 관리함으로써 어쩔 수 없이 하게 된 것이라고 봐야하지 않을까?)

### 05. 복잡한 플로우 만들기 - 2
- Viewless Riblet.
- ViewControllable을 만드는 대신에 dependency로 주입받는다.
- Interactor.didBecomeActive에서 하위 riblet으로 분기.
- Viewless Riblet을 만든 김에 로직을 더 부여하자. 
  - child riblet이 하위 화면으로 이동해야할 때, listner를 통해 Viewless Riblet에게 알리고
  - Sibling으로 child riblet을 추가한다. View Hierarchy 상으로는 손자이지만 router는 자식.
- Parent와 Child의 값 교환. 
  - Parent -> Child 는 ReadOnlyCurrentValuePublisher를 넘겨주는 방식으로.
  - Child - > Parent 는 CurrentValuePublisher를 넘겨주면, child가 send().

### 06. 복잡한 플로우 만들기 - 3
- TopupInteractor에서 EnterAmountRoot 여부를 관리해야 한다. Card가 있으면 true, 없으면 false.
- AddPaymentMethod riblet을 parent에 따라 다르게 동작하도록 수정.

## 2부. 모듈 레벨 아키텍처. 유지 보수와 개발 속도를 고려하는 모듈화

### 01. 모듈화(Modularization)
- 모노리틱 앱 구조.
- 모듈간 뿐 아니라 객체간 단방향 참조만 잘 해도.

### 02. 느슨한 결합
- 객체지향 프로그래밍 이전에는 컴파일 타임 의존성과 빌드 타임 의존성이 동일.
- The power of OO comes from safe, convenient polymorphism. With OO, you have absolute control over the every single source code dependency in your system. - Robert C. Martin, The Future of Programming Languages
- 다형성을 이용해 의존성을 역전.
- 의존성 역전을 남발하면 코드를 이해하기 어려워질 수도 있다.

### 03. 의존성 주입 패턴
- Composition Root. 
- Ribs의 Builder가 생성자 주입 / 메서드 주입 / 프로퍼티 주입.
- 하위의 의존성을 상위 화면들이 모두 챙겨서 주입해줘야하는 상황을 피하는 
- Volatile Dependency
  - Runtime에 초기화가 필요한 것. ex) 데이터베이스
  - 아직 존재하지 않거나 개발 중인 것.
  - 비결정론적 동작/알고리즘.
- Stable Dependency
  - 결정론적 동작/알고리즘.
  - 신뢰할만한 하위호환성.
  - Volatile 의존성을 제외한 모든 것.

### 04. 리팩토링
- Local Swift Package를 이용한 모듈 분리.
- Interface용 모듈의 별도 정의.

### 05. 잘못 설계된 모듈 고치기
- Network 모듈을 적용.
- URLSessionConfiguration.protocolClasses를 통해, response mock을 작성할 수 있다.
- 변경에는 닫혀있고 확장에는 열려있는.

### 06. 플랫폼 팀의 역할과 필요성
- 개발자들을 위한 개발자. 
- CI, 자동화 파이프 라인 관리, 공통 모듈 관리 등.
  - 릴리즈 트레인 방식. 정해진 시간에 계속 배포하는. 
  - 기능 개발과 배포 타이밍을 별개로 가져갈 수 있음.
  - 자동화 파이프 라인이 필수.
- 프로젝트 전체 현황과 정보 공유.
  - 모듈별 커버리지를 랭킹화한다던가...
- 아키텍처 유지, 보수 및 개선을 통한 개발자 경험 향상.

## 3부. 자동화 테스팅

### 01. 테스트의 종류
- Test Pyramid.
  - Manual Tests
  - E2E Tests (End to End. UI Testing)
  - Integration Tests
  - Unit Tests (Include Snapshot Tests)

### 02. Unit Testing
- Interactor 테스트. Presentable, Dependency, Listener의 mock 작성이 필요.
- Async 동작을 테스트할 경우, XCAssert를 그냥 사용하는 것은 어렵다. XCTwaiter.wait()를 사용.
- 핵심 로직과 비동기적 특성을 분리하여, 테스트는 가능한 한 동기적으로 작동하게 만들기.
- Secheduler를 주입 받도록 수정하고, 테스트에서는 Combine.ImmediateScheduler를 사용.

### 03. Snapshot Testing
- Uber와 PointFree 정도의 선택지가 있음.
- https://github.com/pointfreeco/swift-snapshot-testing
- 스냅샷을 생성해야하기 때문에, 첫 테스트 실행은 무조건 실패.

### 04. UI Testing
- AccessibilityIdentifier를 이용해 UI 요소를 찾아냄.
- 테스트가 실제 서버를 사용할 수는 없으니 mocking이 필요한데...
- https://github.com/httpswift/swifter
- 로컬 서버를 돌려주는 방식이기 때문에, 앱에서 분기가 필요.
- (endpoint가 하나 뿐인 GraphQL 서버는 어떻게 해야하는지 확인 필요.)
- 기기의 화면만으로 결과 검증을 해야 함.

### 05. Integration Testing
- 유닛테스트보다는 비싸지만 신뢰도는 더 높은.
- https://github.com/lyft/Hammer

### 06. 자동화 테스트 품질 및 운영
- 스냅샷 테스트의 정확도에 대한 이야기. 5% 정도 여유를 두지만 View에 따라 더 예민해야하는 경우가 있을 수 있음.
- Computed Property. 매번 새로 생성되지만 사용하는 쪽에서는 이를 인지하지 못할 수 있고, 단위테스트에서도 검증이 안될 수 있음.
- 테스트 신뢰도. 간헐적으로 실패할 수 있는 테스트는 한층 더 신중하게 추가해야 함.
- 테스트 용이성. 테스트를 작성하고 관리하는 경험이 쾌적한가.

## 4부. 확장성 있는 인프라: 코드만으로 해결할 수 없는 문제들

### 01. 피쳐플래그
- 코드 변경이나 배포없이 프로그램의 동작을 변경할 수 있게 하는 소프트웨어 개발 기법.
- Bool 플래그에 따라 UI를 변경한다거나, 특정 기능을 활성화하거나 비활성화.
- 피쳐 브랜치를 사용하는 경우, 통합이 되지 못한 상태로 오랫동안 개발하게 되면 여러 가지 문제가 발생할 수 있음. 통합이라던가.
- Trunk Based Development를 사용하면서, 피쳐플래그를 이용해 작은 단위로 바로 통합을 진행.

### 02. 품질 모니터링(QEM)
- 사용자들이 앱을 잘 쓰고 있는지, 실시간 사용 경험은 어떤지.
- 성공률 / 소요 시간 / 에러 종류
- 대시보드를 주기적으로 들어가서 구경하고 있지 말아라. 대시보드를 언제 들여다 봐야 하는지 알려주는 경보를 만들어라.
- 일이 터졌을 때, 일찍 알아차릴 수 있는 시스템을 구축하는 것.

### 03. 팀워크, 평가, 커리어
- 누군가는 손이 빠르거나, 설계를 잘 하거나, UI를 매끄럽게 잘 만들고, 멘토링을 잘 하고, 분위기 메이커 역할을 하고.
- 자신과 팀을 분리해서 생각하면 누구도 성장하기 힘듭니다.
- 문제와 사람을 분리하고 문제 자체를 깊게 파고 들어야 합니다.
- 부정적인 피드백을 조직장과의 면담 때 돌아보고, 또 같은 상황에 처했을 때 어떻게 할 것인지에 대해 짚어봄.

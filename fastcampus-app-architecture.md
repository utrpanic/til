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
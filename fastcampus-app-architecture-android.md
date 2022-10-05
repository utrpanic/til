# [강사룡의 앱 안정성 및 확장성 강화를 위한 Android 아키텍처](https://fastcampus.co.kr/dev_red_ksr)

## Part 0. Introduction

### 1. Introduction
- 구글 안드로이드 엔지니어링 부서 디벨로퍼 릴레이션스 엔지니어.
- 왜 아키텍처가 중요한가. 비즈니스를 촉진한다.
- (슬라이드가 너무 빨리 지나가는 것 같다.)
- MVx는 UI 계층.
- 모던 안드로이드 아키텍처에서는 멀티 모듈이 거의 필수.

## Part 1. 모바일 아키텍처 개론

### 1. 복잡성 제거 - 좋은 설계를 위한 첫걸음
- 좋은 아키텍처란. 풀려는 문제에 잘 어울리는 설계.
- 이해하기 쉽고, 테스트하기 쉽고.
- 복잡성이란. 이해하기 어렵고 수정하기 어렵게 만드는 소프트웨어 구조에 관련된 모든 것.
- 어떻게 낮출 것인가. 불필요한 정보를 감추기. 깔끔한 추상화.
- 추상화 사이의 경계 찾기. 정보가 공유된다. 함께 있는 것이 인터페이스를 단순하게 만들어 준다. 그렇다면 합치기.

### 2. 좋은 아키텍처를 위한 원칙 - SOLID 원칙
- 중간 규모? 클래스끼리 모여있는 모듈 구조.
- 중간 규모의 소프트웨어 구조가 좋은 설계 요구 사항을 만족시키는 것. 그것이 SOLID의 목표.
- 단일 책임 원칙. 책임이란 특정 업무나 일이 아니라... Facade 패턴으로...? 행동을 유발하는 actor가 하나.
- 인터페이스 의존의 원칙. 자신이 사용하지 않는 것에 의존하면 안된다.
- 개방-폐쇄 원칙. 클래스/모듈 사이의 의존 관계에서도 유효한 원칙.
- 리스코프 치환 원칙. 바바라 리스코프. 그치만... 상속보다 조합.
- 의존성 역전 원칙. 높은 수준의 코드는 낮은 수준의 세부 사항에 의존하면 안된다.

### 3. 모바일 클린 아키텍처
- 모바일 클린 아키텍처라는 것이 실제로 있는 것은 아님. 여러가지 원칙들과 여러가지 조언들의 결합에 가깝다.
- '소프트웨어 아키텍처는 선을 긋는 기술이며'. 캬~
- 공통 폐쇄 원칙(Common Closure Principle) = SRP(단일 책임 원칙) + OCP(개방-폐쇄 원칙)
- 각 계층은 하나의 (큰) 액터만을 가짐. (액터의 개념이 좀...)
- 가장 높은 수준의 계층은 도메인 계층.
- 엔티티. 데이터 저장소를 얘기하는 게 아니라 업무 규칙을 갖고 있는 업무 룰.
- 유스케이스. 비즈니스 유스케이스. 기술적인 단어가 아님.

### 4. Google에서는 어떻게 좋은 설계를 만들어내는가
- 아키텍처를 공개는 할 수 없고. 어떤 식으로 도출해내는가? 어떤 문화를 갖고 있는가?
- 모든 것이 문서에서 시작해서 문서로 끝난다. 아이디어가 있으면 일단 문서로 작성하고 그 다음에 공유.
- 피드백을 통해 TDR(Technical Design Document) 등으로 정제.
- 인사 평가에서 그대로 첨부자료(Artifact)로 사용됨.
- 두 번 설계 하라! 적어도 2개 이상의 설계를 제안해야 함. 똑똑한 사람들에게는 더욱 어려움. '인내할 것'.

## Part 2. 테스트 구현

### 1. 좋은 아키텍처를 위한 올바른 테스트
- 테스트에 대한 기본적인 원칙들. 
- 아키텍처를 수정할 때마다 많은 변경이 수반되고. 확신을 갖고 수정할 수 있는지.
- 인터페이스의 분리. 필요없는 의존성의 제거.
- 협업을 촉진한다. 의도를 보여주니까. 의도를 벗어나면 테스트가 실패할테니까.
- 테스팅 피라미드. Unit Test 70%, Integration Test 20%, End to end Test 10%.
- Firebase test lab이라던가. 편리한 툴이 있어서 End to end Test가 너무 많아지는 것은 anti pattern.
- Android의 경우. Local이냐 아니냐. 
  - Local Test. Robolectric 같은 걸로 Context를 주입받을 수도 있고.
  - Instrumented Test. Espresso 같은 라이브러리를 사용.
- 무엇을 테스트해야하는가. 가장 어려운 부분.
  - 의미있는 테스트가 되어야 함. Edge cases는 좋은 예.
- 실제 구현체를 쓰거나, fake를 쓰거나, mock/spy/stub. 이 순서대로 시도.
- Mock을 쓰면 가장 좋은 것은 isolation. Realism이 떨어질 수 있음. Mock 구현 자체에 버그가 있을 수 있고.

### 2. Google은 어떻게 테스트하는가
- 2005년. 무려 80% 이상의 PR이 버그로 인해 롤백되었음.
- 장애가 발생하면?
  - 해당 PR을 통째로 롤백. (PR이 충분히 작고 분리되어 있어야 함.)
  - 버그에 대한 테스트 작성.
  - 테스트를 통과 시킬 수 있도록 수정.
- 테스트. Beyonce Rule. If you liked it, then you should put a test on it.
- 정확성(Correctness). 명확성(Clarity). (연관되지 않은 변경 사항에 대한)안정성(Resilience).
- 유용성(Helpfulness). 
- 외부 의존성 처리의 원칙. 가능하면 실제 코드를 사용. Mock 보다는 fake를 사용.
- Snapshot Test. 깨지기 쉬운 테스트가 될 가능성이 농후하므로, 목적 설정이 아주 중요. View 부품의 단위들에 대한 스냅샷. 화면 전체에 대한 스냅샷 테스트를 하는 것은 아님.

### 3. Google의 테스팅 Best Practices 1
- 일단 대원칙은 '테스트 불변성(Unchanging Test)'.
1. Test via Public APIs. 테스트에서 사용하기 위해 access level을 변경해선 안됨.
2. Test State, Not Interactions.
3. Make Your Tests Complete and Concise. 테스트하고자 하는 것을 정확히 알 수 있는 정보를 모두 갖고 있어야 한다. 그 외의 것들은 최대한 감춰야한다. (이게 지금 우리에게 필요해!)

### 4. Google의 테스팅 Best Practices 2
4. Test Behaviors, Not Methods. Method와 테스트 케이스의 비율은 1:n.
5. Structure tests to emphasize behaviors. GIVEN / WHEN / THEN. When / Then 은 반복되는 케이스도 있음.
6. Don't Put Login in Tests. 그 로직이 버그를 가질 수도 있으니까.
7. DAMP(Descriptive And Meaningful Phrases), Not DRY. 반복 코드를 함수로 만든 것이 지금까지의 원칙을 해쳐선 안된다.
8. No Shared Value. Mutable 객체의 경우, 더욱 위험.
9. Shared Setup. Setup에서는 진짜 default에 해당하는 것만.

## Part 3. UI 계층

### 1. MVx의 대원칙
- 어떤 경우든 Model은 분리되어야 한다.
- 뷰의 역할을 할 수 있는 한 분리시켜야 한다.
- Android에서 발생할 수 있는 특수한 상황. (Context라던가 생명주기 등)
- Controller. 어떤 View를 보여줄 것인가. 에러는 어떻게 표현할 것인가.
- Android에서 MVC에 대해 좋은 이야기는 못 들어본 것 같다. User event 외에도 Controller가 처리할 것이 너무 많음.
- UI와 로직을 분리하기도 어렵고. 생명주기 내에서 Controller는 어떻게 변화해야하는가.
- 그러다보면 fat activity, fat fragment... Unit test 작성도 까다로워짐.
- 해결책은?
  - View를 분화. View 마다 ViewController를 제공.
  - Activity/Fragment는 ViewController에게 여러 위임하는 역할. Life cycle 관련 처리도 위임.
- 그래도 Context가 계속 문제. 그래서 뷰와 컨트롤러를 완전히 분리해야 한다. non-MVC가 필요하다!

### 2. MVP, 그리고 non-MVC에서 공통적으로 고려해야할 것들
- Model-View-Presenter. 뷰는 비즈니스 로직에 관련된 부분에 관여할 수 없도록 분리. Presenter로 넘김.
- 수동적인 뷰(Passive View): View는 완전한 Humble Object여야 한다. 최대한 dumb 하게.
- Fat Activity/Fragment를 방지해주고. 테스트 가능성도 증대. Presenter가 Context를 사용하지 않기 때문에.
- MVP. 심플하지만, 잘못 구현하기도 쉽다. 또한, Presenter와 View의 상호 참조 문제도.

## [Architecting for Analytics, Remote Config, DTOs, Custom vs Primitive Types](https://www.youtube.com/watch?v=s3crpkXI4vA)
- Cross-cutting concern. Decorators in the Composition Root.
- 일단 ViewController에서 직접 analytics를 호출하는 것을 피하고.
- DelegateComposite. 생성자로 [Delegate]를 받는다. Interception!
- Remote Config. 값을 가져올 때까지 사용자를 기다리게 할 것인지 cache를 일단 사용할 것인지는 비즈니스 상의 중요도에 따라 결정한다.
- Custom vs Primitive Types. 필요하다면 Custom Type으로 감싸고, 그를 초기화할 때 방어 코드 처리 같은 걸 하면 된다.

## [Chaining dependent network requests in Swift with Combine](https://www.youtube.com/watch?v=fCuBe6T6sK0)
- Combine을 쓰면 되지 않겠는가.
- 첫 request의 결과에 따라 다음 request를 분기하게 되는 경우.
```Swift
func loadUser() -> AnyPublisher<User, Error>
func loadDetails(user: User) -> AnyPublisher<User, Error>
func loadFriends(user: User) -> AnyPublisher<[Friend], Error>
func loadUserDetails() -> AnyPublisher<(UserDetails, [Friend]), Error> {
    loadUser().flatMap { user in 
        Publishers.Zip(loadDetails(user: user), loadFriends(user, user))
    }.eraseToAnyPublisher()
}
```

## [Unit tests with RxSwift/Combine & Model/ViewModel separation best practices](https://www.youtube.com/watch?v=1SUFMcYjCpE)
- ViewModel이 CurrentValueSubject를 private으로 갖고 있고, AnyPublisher를 노출하는 구현으로 시작.
- Expectation/wait을 이용해서 async 처리.
- ValueSpy. AnyPublisher를 받아서 event를 모두 기록. 하지만... Publisher가 synchronous하게 주기 때문에 동작하는 것 아닌지.
```Swift
private class ValueSpy {

    private(set) var values = [String]()
    private var cancellable: AnyCancellable?

    init(_ publisher: AnyPublisher<String, Never>) {
        cancellable = publisher.sink(receiveValue: { [weak self] value in
            self?.values.append(value)
        })
    }
}
```
- Presentation Logic과 Business Logic의 경계는 어디가 되어야 하는가.
- Presentation Logic은 View Model로 Business Logic은 Model로.
- Codable도 Business Logic이라고 부르기 어렵다. Codable도 레이어로 분리하는 것이 좋다.

## [From MVVM to Clean Architecture: Core Data Transaction Consistency](https://www.youtube.com/watch?v=5MCNR4u12k8)
- MVVM에서 Clean Architecture로 가려고 결정한 이유가 무엇인가.
- CoreDataChannelRepository: ChannelRepository
- CoreDataChannel: Channel 
- Business logic과 Infrastructure를 분리. Business logic은 Channel과 ChannelRepository만 다룸.
- Channel protocol이 필요한 이유는 아마도 CoreDataChannel이 NSManagedObject를 상속해야 해서.
- 그럼 이제! How to implement transaction consistency. 
- TransactionDecorator와 UseCase가 모두 SendMessageInput을 conform. Decorator가 UserCase를 decoratee로 들고 있으면서 SendMessageInput 인터페이스 호출 시 decoratee의 동일 인터페이스 호출.
- 구현체가 NSManagedObjectContext를 받아서, failure 상황에서 rollback() 호출.
- Frameworks(Like CoreData) -> Infrastructure Adapters -> Application Login -> Domain
- Clean Architecture, Domain-Driven Design, Implementing Domain-Driven Design, Dependency Injection
- https://www.essentialdeveloper.com/book-suggestions

## [Multithreading and Concurrency in iOS apps](https://youtu.be/G6cVUcHre8Y)
### Threads and Concurrency. 
- 명시적으로 multi-threading 구현을 하지 않더라도, 내부적으로 multi-thread 환경에서 개발하고 있다.
```Swift
Thread.detachNewThread { } // 이런 게  있었다고;;;
```
- What's the enemy of concurrency?
  - Mutable shared state
### Delegate / Closure
- Caller와 handler를 decouple하고 싶다면 delegate를 사용할 수도 있다.
- UITableViewDiffableDataSource는 closure 기반 인터페이스. 이런 게 또 있었네(iOS 13이상).

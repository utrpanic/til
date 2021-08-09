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

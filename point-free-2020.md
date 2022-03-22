# [Episode #87 The Case for Case Paths: Introduction](https://www.pointfree.co/episodes/ep87-the-case-for-case-paths-introduction)
- KeyPath. enum은 그에 준하는 무언가가 있는가.
- 없으니까! 일단 `CasePath` struct를 정의해봅니다.
```Swift
struct CasePath<Root, Value> {
  let extract: (Root) -> Value?
  let embed: (Value) -> Root
}
extension Result {
  static var successCasePath: CasePath<Result, Success> {
    CasePath<Result, Success>(
      extract: { result in
        if case let .success(success) = result {
          return success
        }
        return nil
    },
      embed: Result.success
    )
  }
  static var failureCasePath: CasePath<Result, Failure> {
    CasePath<Result, Failure>(
      extract: { result in
        if case let .failure(failure) = result {
          return failure
        }
        return nil
    },
      embed: Result.failure
    )
  }
}
```
- 하지만 `Result`의 static var인 것도 이상하고, boilerplate라던가... 

# [Episode #88 The Case for Case Paths: Properties](https://www.pointfree.co/episodes/ep88-the-case-for-case-paths-properties)
- KeyPath가 제공하는 것 중, `appending(path:)`를 CasePath도 제공하고자 한다.
```Swift
struct User {
  var id: Int
  var isAdmin: Bool
  var location: Location
  var name: String
}
struct Location {
  var city: String
  var country: String
}
(\User.location).appending(path: \Location.city)
\User.location.city
```
```Swift
extension CasePath/*<Root, Value>*/ {
  func appending<AppendedValue>(
    path: CasePath<Value, AppendedValue>
  ) -> CasePath<Root, AppendedValue> {
    CasePath<Root, AppendedValue>(
      extract: { root in
        self.extract(root).flatMap(path.extract)
    },
      embed: { appendedValue in
        self.embed(path.embed(appendedValue))
    })
  }
}
func .. <A, B, C>(
  lhs: CasePath<A, B>,
  rhs: CasePath<B, C>
) -> CasePath<A, C> {
  return lhs.appending(path: rhs)
}
```

# [Episode #89 Case Paths for Free](https://www.pointfree.co/episodes/ep89-case-paths-for-free)
- Reflection. `Mirror.children`의 `label`과 `value`를 사용.
- Reflection을 사용할 경우, compiler의 지원이 충분하지 않을 수 있기 때문에, 테스트 같은 것으로 보완해야 한다.
```Swift
func extract<Root, Value>(
  case: @escaping (Value) -> Root,
  from root: Root
) -> Value? {
  let mirror = Mirror(reflecting: root)
  guard let child = mirror.children.first else { return nil }
  guard let value = child.value as? Value else { return nil }

  let newRoot = `case`(value)
  let newMirror = Mirror(reflecting: newRoot)
  guard let newChild = newMirror.children.first else { return nil }

  guard newChild.label == child.label else { return nil }

  return value
}

extract(case: Authentication.authenticated, from: auth) // AccessToken
extract(case: Authentication.authenticated, from: .unauthenticated) // nil
extract(case: Result<Int, Error>.success, from: .success(42)) // 42
```
- `..`, `^`, `/` operator들을 추가로 정의해서, `if case let`이 필요한 많은 부분들을 더 정리하였다.
- 새 operator들이나 `CasePath`를 사용한 인터페이스들을 보면, 코드 가독성 관점에서는 code generation이 더 나을지도 모르겠다는 생각이 슬몃 드는데... 일단 다음 에피소드에서 더 확인해볼 것.

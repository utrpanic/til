# [How safe are Swift structs?](https://youtu.be/3zSuAkIt9jY)
- struct가 기본이고 꼭 필요한 곳에만 class를 사용하라고 하지만 동의하지 않는다.
- struct의 mutation이 더 제한적인 부분은 분명 좋다.
- 제대로 사용하지 않는다면 가능한 모든 곳에 struct를 사용한다고 해서 안전해지지 않는다. 특히 struct가 class property를 갖게 될 경우.
- Value에 대해서만 사용하는 것이 좋겠다.
- 해당 타입이 behavior를 갖는다면/사이드이펙트를 유발한다면 value가 아니다. class로 정의하거나 value만 남기고 나머지 기능을 다른 class로 작성하는 것이 좋겠다.

# [Many Advanced Developers Forget This](https://www.youtube.com/watch?v=nh5LipqIt4g)
## Object
- Reference Type, Encapsulation, Polymorphism
- Class, Initializer, State
- class 키워드 없이 상속을 구현하는 방법.
- 필요한 인터페이스를 closure property로 정의하고, 객체 생성 시 바꿔넣는 방법.

# [#1 Reason Why You Don’t Improve As a Software Developer](https://www.youtube.com/watch?v=csm6kK8jUkY)
- 시간과 성장그래프에서 Plateau에 도달한다. 뭘 모르는지 아는 단계와 뭘 모르는지 모르는 단계가 사이의 어디쯤. 여기서 정체가 온다.
- Comfort zone(Good enough). 대부분이 여기에서 더 성장하지 못하는.
- Uncomfortable zone에 왜 들어가는가. 자전거를 처음 배울 때, 걷는 것보다 느린데 왜 배우느냐고 말하는 사람이 있는가.

# [“How do you think when writing tests?” – It’s simpler than you may think](https://www.youtube.com/watch?v=9CyIrbCNWPs)
- 생각나는 모든 것을 커버하려고 하나요? 아니면 우선순위 리스트가 있나요? 혹은 그냥 가능한한 커버를 하려고 하는 건가요?
- Writing a failing test -> Make the test pass -> Refactor
- 실패한 테스트를 성공시키기 위해 너무 많은 코드를 작성하는 것은 문제.
- 테스트를 성공하면 리팩토링하기 전에 커밋하세요. 필요할 경우 revert가 쉬워집니다.
- 테스트 대상을 sut로 이름 붙이기.
- 테스트는 디자인을 결정하지 않는다. 개발자가 해야 한다.
- 문제를 더 작은 문제로 쪼개는 것.
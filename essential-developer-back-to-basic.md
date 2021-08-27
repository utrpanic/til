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

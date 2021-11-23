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

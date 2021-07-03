## [SwiftUI + UIKit + RxSwift + Combine + DDD = ?](https://tv.naver.com/v/19397822)
- 2021.04. 최종민님. 클로바노트.
### 1. 클로바노트?
- NEST(Nearest End-to-end Speech Transcriber)를 이용한 음성 기록 서비스. 듣고 스크립트화 해주는 서비스.
### 2. DDD(Domain Driven Design)
- 현실의 문제를 코드로 반영하기 위한 코드 디자인 방식.
### 3. SwiftUI & Combine
### 4. 적용
- ViewModel에서 Combine-Rx 브릿징 처리를 하는 방식을 사용. 그 밖엔 Rx로.
### 5. 이슈 / 문제점 / 개발 시 주의사항
- UIViewRepresentable. Coordinator를 선언 후 사용.
- .id(UUID()). 뷰를 새로 생성하는 조건. 같은 값을 넣으면 불필요한 갱신을 일시적으로 막을 수 있음.
- SwiftUI의 Crash Trace 추적 불가.
- View 갱신은 각 property의 willSet에서 발생.
- iOS 14부터는 키보드 영역이 safeArea로 들어감.
### 6. 감상 및 결론
- iOS 13.4 미만에서는 많이 불안정.

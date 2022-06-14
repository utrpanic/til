## [UIKit으로 만들어진 토스 디자인 시스템, SwiftUI에서 쓸 수 있을까?](https://www.youtube.com/watch?v=q0CX-0k2l0g)
### 강준구님
- TDS. 
- UICollecitonViewCell의 경우. CellItemModelType protocol, FlowSizeable protocol을 model이 conform. 
- CollectionViewAdapter. Model의 `hash()`가 변경되면 cell이 업데이트됨.
- Component를 이용해 UIView를 추상화해 사용하고 있는데, Component가 SwiftUI.View를 conform하면!!!
- Design Syntax Tree. Server Driven UI?

## [iOS앱을 매주 배포 한다고?](https://www.youtube.com/watch?v=GhcJ-KDFLrU)
### 김준모님
- iOS 앱을 매주 배포.
- 클라이언트 플랫폼팀. 개발자를 위한 개발자.
- 아키텍쳐 구성, 모범 사례 작성. 빌드 속도 개선. 자동화. 선행 기술 연구.
- 크로스 플랫폼 규격 정의. (JSON이나 ProtoBuf 같은 것인가!)
- 입사 첫 날 실행해야하는 한 줄의 스크립트! 개발 환경 설정.
- Push에 `진행중`, PR에 `승인 대기`, Merge에 `승인됨`.
- Feature branch가 필요한 경우, 매일 새벽 열려있는 PR을 기준으로 back merge 진행.
- PR label에 따라 자동으로 머지를 한다거나 각각 다른 자동화.
- 매일 새벽마다 nightly 빌드가 배포.

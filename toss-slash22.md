## [UIKit으로 만들어진 토스 디자인 시스템, SwiftUI에서 쓸 수 있을까?](https://www.youtube.com/watch?v=q0CX-0k2l0g)
### 강준구님
- TDS. 
- UICollecitonViewCell의 경우. CellItemModelType protocol, FlowSizeable protocol을 model이 conform. 
- CollectionViewAdapter. Model의 `hash()`가 변경되면 cell이 업데이트됨.
- Component를 이용해 UIView를 추상화해 사용하고 있는데, Component가 SwiftUI.View를 conform하면!!!
- Design Syntax Tree. Server Driven UI?

## [UX와 DX, 그 모든 경험을 위한 디자인 시스템](https://www.youtube.com/watch?v=5WBlhIl8KkY)
### 박민수님
- Design Platform Team. 
- 안드로이드 UX 엔지니어가 개발자 경험을 포함해서 디자인 시스템을 다룬다.
- TDS Kotlin DSL(Domain Specific Language).
- 보이스톤 메이커. 일관성 있는 토스 보이스톤 유지를 위한 규칙을 제공. Framer에서 돌아간다고.
- 불필요한 한자어 표현. 글자 수 줄이기. 어미 통일.
- 개발이 완료된 후에는 또 누락이 시작될 수 있어서... lint로도 개발.
- 디자이너/개발자 외에도 이를 사용할 수 있도록 In-App Overlay 보이스톤 메이커 개발.
- 보이스톤 규칙은 서버에서 가져옴. 제목인지 설명인지 등 컴포넌트 타입에 따라 규칙이 다름.
- Design Syntax Tree. 디자인된 화면을 기계가 구별할 수 있는 syntax tree.
- 하나의 DST로 모든 플랫폼에서 동일한 화면을 구현할 수 있고, lint도 가능.
- DST를 Server Driven으로 배포하면, 앱 업데이트 없이도 디자인을 변경하는 것이 비전.
- DST를 통해 코드를 자동으로 생성. DST JSON을 복사해서 붙여넣으면 TDS Kotlin DSL로 변환됨.

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

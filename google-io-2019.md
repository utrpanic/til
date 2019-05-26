## [Google Keynote](https://youtu.be/lyRPyRKHO8M)
- Building a more helpful Google for everyone. 일관된 메시지. 일부가 되어 일하는 게 무척 자랑스러울 것 같다.
- 이미지에서 텍스트를 뽑아내고 번역하고 읽어주고.
- 'Hey Google' 없이 연속된 명령 가능.
- 동영상에서 나오는 말소리를 실시간으로 STT. 2G짜리 모델을 80MB로 만들었고, 덕분에 네트워킹 없이 로컬에서 사용 가능한 수준.

## [Developer Keynote](https://youtu.be/LoLqSbV1ELU)
- Android에서 입앱업데이트 기능을 지원. 업데이트 하라며 팝업을 띄우는 건데, 업데이트를 하러 구글플레이로 가지 않고, 앱을 그대로 사용하면서 다운로드 진행. 설치도 나중에 함.
- ML Kit을 Firebase를 통해서 제공하기 때문에 iOS에서도 사용 가능.
- 학습된 모델을 앱이 다운로드 받아서 온디바이스로 동작하게 해줌.
- Flutter가 웹을 대응함으로써 모든 플랫폼에서 사용 가능. NewYorkTimes에서 퍼즐앱을 이 방식으로 구현했다고. 애니메이션 같은 게 없고 UX가 단순한 기능성 앱에 유용할 거란 생각은 개인적인 편견이었던 것으로.

## [Beyond Mobile: Material Design, Adaptable UIs, and Flutter](https://youtu.be/YSULAJf6R6M)
- Material Design을 채용하니 앱들이 다 비슷비슷하지 않은가에 대한 대답...을 하겠다고 했지만 신통치는 않음.
- DarkTheme 제공. 설정에서 바꿀 수 있는 건가?
- Slider를 SliderTheme으로 wrapping 하면 된다고.
- Semantic이라는 Widget이 추가되었고, 이전 위젯들은 semanticLabel이 추가됨. 이게 이제 나오다니 그 전에는 accessibility를 지원하지 않았던 건가.
- Widget에 .adaptive()를 붙이면, Material/Cupertino를 런타임에 결정해준다. 사실 점점 한물간 이야기처럼 들린다. 회사에 디자인 리소스가 부족해서 이런 생각을 하게 된 것인지. 둘을 각각 디자인할 리소스도 없고, 각각 디자인한다고 하면 .adaptive() 정도로 넘어갈 수 있는 정도가 아닐 것이다.
- Interactive/Passive experience. TV App 이야기를 하면서.
- Relative Text Size. 어느 디바이스에 봐도 비슷한 크기로 보이도록 하겠다는 건 안드로이드의 그것인데, 디바이스에 따라 디바이스와 사용자간의 거리가 또 다르기 때문에 그것만으로는 부족하다는 이야기. _isPassive로 분기.
- 패드의 경우, Firebase ML 기능을 이용해, 카메라에서 사용자가 가까우면 Interactive 모드, 멀면 Passive 모드로 바꿔준다고. 와 정말 생각 많이한다.
- Phone, Web 뿐 아니라 각종 디바이스(Digital Wall이 나옴)에도 가능.

## [Building for iOS with Flutter](https://youtu.be/ZBJa-xjZl3w)
- Cupertino package 소개. 새삼스러울 것도 없는 세션으로 플랫폼을 보고 Material과 Cupertino를 일일이 분기하는 코드를 선보임.
- 플랫폼별로 아예 디자인을 달리하는 경우는 논외고, 하단탭 스타일을 햄버거 스타일을 한 코드로 제공하는 것은 가능.
- 그외에 플랫폼의 기본 위젯을 그대로 사용하는 경우가 잘 없어서, 개인 프로젝트에서 재미로 구현하는 정도가 될 듯.
- Material에서는 shadow를 Cupertino에서는 flat design.
- Native Code의 함수를 호출하거나 Event Channel을 여는 것이 가능. 소셜 로그인 같은 걸 구현할 때 쓸 수 있을 듯.
- Dart 패키지 중에 path_provider. Flutter팀에서 유지보수하는 패키지로, 플랫폼 별로 로컬 디렉토리를 얻어낼 때 사용할 수 있음.

## [Beyond Mobile: Building Flutter Apps for iOS, Android, Chrome OS, and Web](https://youtu.be/IyFZznAk69U)
- Android Studio를 쓸것인지 VS Code를 쓸 것인지. 다른 영상에선 VS Code를 쓰던데, 여기선 Android Studio를 쓰고, View Hierarchy Inspector를 사용하는 것을 보여줌.
- 애니메이션은 60frame이라고.
- 다양한 스크린 사이즈에 대응하는 방법은, 가로 길이를 보고 분기. 좀 하찮아보이긴 하지만 별다른 방법이 없는 것도 사실.
- logicalKeyId라는 게 있어서, 키보드 타입이나 언어와 상관없이 특수키들을 처리할 수 있음. (KeyCode.backspace 등)
- 크롬OS의 경우, Android Runner를 돌리고 그 위에 Flutter를 돌림.
- C++ Flutter engine 그 위에 Flutter framework in Dart 그 위에 앱 코드.
- Flutter for web은 technical preview 상태. Browser 위에 Dart2js compiler 위에 Flutter Web engine 위에 Flutter framework in Dart. 결국 JavaScript로 변환해서 브라우저에 돌리는 방식. 다른 플랫폼과는 확연히 다름. 괜찮은 건지 잘 모르겠음.
- Web의 경우, DartDevTool을 이용해서 Inspector를 사용하는 것을 보여줌.
- (이런 류의 기술은 기술 자체의 서포트보다 멀티 플랫폼을 개발자가 동시에 고려하면서 일하는 게 더 어려워보임. 게다가 Web은 한참 멀리 가버린 듯한 느낌.)

## Dart: Productive, Fast, Multi-Platform - Pick 3

## Pragmatic State Management in Flutter

## Zero to App: Live Coding a Cross-Platform App on Firebase

## Getting Started with TensorFlow 2.0

## Swift for TensorFlow

## Smart Home 101: How to Develop for the Connected Home

## Local Technoloties for the Smart Home

## Tools for Building Better Smart Home Actions



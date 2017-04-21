# [Alamofire](https://github.com/Alamofire/Alamofire)

## DataRequest.validate()
 4.0에서 추가된 것인지 원래 있었던 것인지 모르겠다. response를 validate해서 200...299인 경우 success로, 그 외의 경우는 failure로 보내준다. 다시 말해, 이 함수를 호출하지 않으면 정상적인 response는 status code가 200...299가 아니어도 success로 보내게 된다.
 이 문제로 jack과 약간 의견을 나눠봤는데, network error를 아예 퉁치고 domain error는 success에서 다 처리하겠다고 하면 validate()을 따로 호출하지 않는 것이 맞는 것 같다. 추상화 레벨에 따라 선택권을 준 것으로 생각해야 할 것 같다.


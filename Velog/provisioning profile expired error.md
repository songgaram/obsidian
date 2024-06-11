때는 바야흐로 오랜만에 평소와 같이 자동화 테스트를 위해 xocde로 연결 중이었는데,,,,
테스트를 실패하였다는 문구와 함께 아래 error message를 보았다.


## Provisioning profile expired


> `Failed to install embedded profile (This provisioning profile has expired.) Verify that the Developer App certificate for your account is trusted on your device. Open Settings on the device and navigate to General -> VPN & Device Management, then select your Developer App certificate to trust it.

너의 provisioning profile이 만료가 되었으니 확인해보셈 ㅇㅅㅇ
<br>

## app store connect

![](https://velog.velcdn.com/images/songgaram/post/92182e42-1227-477e-ba4d-3060e800a9ac/image.png)

곧바로 app store connect로 달려가 [certificates](https://developer.apple.com/support/certificates/) 쪽을 확인하였다.


![](https://velog.velcdn.com/images/songgaram/post/5f9eee99-37fb-44b8-9c23-a6f92292a32f/image.png)

app store connect에서는 등록된 개발자 아이디에 관한 정보를 볼 수 있다. 물론 만료일도 확인이 가능하다.
그런데 띠용? 만료 날짜는 넉넉한디요ㅠㅠ
<br>

## Xcode

본디 테스트를 위해서는 초기에 Xcode profile을 등록해야 하고 나는 이전에 Xcode > Target에서 아래 항목의 bundle identifier를 유니크 값으로 지정해 준적이 있다. ([관련 포스팅](https://velog.io/@songgaram/WebDriverAgent-설치))


- WebDriverAgentRunner
- WebDriverAgentLib
- IntegrationApp


그래서 나는 계정 만료일이 문제라면 profile을 다시 생성해서 bundle identifier을 다른 걸로 지정하기를 시도해보았다!

>그리고 그 결과

![](https://velog.velcdn.com/images/songgaram/post/3cec3b9d-1f15-451c-a86f-1c65aad3d242/image.png)

.......
**_Expires in 6days?_**



![](https://velog.velcdn.com/images/songgaram/post/0d8317e8-ff5b-4b27-ab16-7d546d54f902/image.png)

뭔소리... iOS Team Provisioning Profile은 created May 31, 2024 인데 (당시 프로필을 다시 생성한 날짜) 그럼 보통 1년 후 만료되는게 정상인데 6일 밖에 안남았다는 거지...


<br>
이때부터 갖은 방법을 찾아보았다.

- 기기에 연결된 Provisioning Profile 확인해보기..
- 다른 계정 로그인하기..

그러나 "''''모두 실패!!!'''''"


<br>
하지만 직장인은 포기할 수 없다.. 다음 방법으로 해결하였다.
<br>

## 개인 ID가 아닌 iOS Team 계정으로 설정하기



![](https://velog.velcdn.com/images/songgaram/post/7460f379-47b4-4e1f-bd05-7096c274eb6d/image.png)


원래는 Signing & Capabilities의 Team 항목에서 팀계정에 등록되어 있는 내 ID를 선택되어 있었는데 이를 Team 계정을 바꾼 후 bundle identifier도 새롭게 바꾼 값이 아닌 이전 값을 넣어주니 


![](https://velog.velcdn.com/images/songgaram/post/99bea74e-5c21-4912-9148-e8bc6cf06d5a/image.png)

이렇게 정상적으로 내 계정을 찾아 등록이 되는 것을 볼 수 있다.

<br>
여담으로 내 개인 계정을 선택하지 않았는데도 등록이 자동으로 되는 것이 궁금했는데 bundle identifier 값으로 developer를 찾아내는 것이라고......
<br>


### 오늘의 교훈.
- 함부로 계정의 bundle identifier를 변경하지 말자.
- 만료가 되었다면 계정을 지우고 다시 생성하자.


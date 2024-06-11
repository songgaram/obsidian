# Touch Action의 시대가 저물었다.

appium에서 touch action을 사용하여 드래그, 롱프레스 등 여러 동작들을 실행시키던 나는 어느날 한 에러 메시지를 마주하게 된다..



## Touch Action 사용 시 에러 메시지

> `selenium.common.exceptions.WebDriverException: Message: Unhandled endpoint:`
`UnknownCommandError: Unhandled endpoint`



엥? 하며 구글링을 해보니 [나와 같은 문제를 겪는 사람](https://discuss.appium.io/t/unsupportedcommandexception-unhandled-endpoint-error-displayed-when-performing-touch-operations/41916/3)을 보았다.

답변을 요약하면 `Touch Action` 사용하려고 하니 에러가 발생함 → 해당 메소드는 **deprecated** 되어서 `W3C actions`을 사용해야 함



![](https://velog.velcdn.com/images/songgaram/post/419cf158-0fe1-4ada-891d-cb2882dc57d3/image.png)

아래는 `Touch Action`을 사용하면 항상 뜨는 warning 메시지이다.

`deprecated:: 2.0.0
        Please use W3C actions instead: http://appium.io/docs/en/commands/interactions/actions/
 "[Deprecated] 'TouchAction' action is deprecated. Please use W3C actions instead.”`


그동안 나는 그래도 동작은 잘하잖아~ 라며 해당 메시지를 못본척 하고 있었다^^.....

![](https://velog.velcdn.com/images/songgaram/post/fddb5464-652d-4349-bb21-cd05cce2908d/image.jpeg)



하지만.. 이제 그를 놓아줄 때가 왔다... 그래.. 우리에겐 뭐가 있다? 바로 공식에서 준…..답… `selenium` 
<br>
<br>


# [selenium] ActionChains

> https://www.selenium.dev/documentation/webdriver/actions_api/

공식이 하라는 대로 따라했음

근데 아래 에러 메시지 발생함 ㄱ-...

## ActionChains 사용 시 에러 메시지

> UnknownError: An unknown server-side error occurred while processing the command. Original error: Error Domain=com.facebook.WebDriverAgent Code=1 "It is mandatory to have at least one item defined for each action. Action with id 'key' contains none" UserInfo={NSLocalizedDescription=It is mandatory to have at least one item defined for each action. Action with id 'key' contains none}



어쩌라는 말이냐 ㅠㅠ 이때부터 서버 url쪽 확인하기 시작했다..

### 1) appium migration

appium1 → appium2로 마이그레이션 되면서 base path가 의미가 없으니까 If you want to retain the old behaviour하고 싶으면 you can set the base path via a command line argument에 이렇게 써라

`appium --base-path=/wd/hub`

→ 했으나 해결 안됨

[관련 페이지](https://appium.io/docs/en/2.0/guides/migrating-1-to-2/)

### 2) 버전이 문제인가? ㄴㄴ

appium —version

>2.2.1

npm update -g appium

appium —version

>2.5.4

→ 했으나 해결 안됨

<br>
<br>

### 3) 🚨 오 나랑 같은 pause 문제 겪는 깃헙 이슈 발견!!!!

- [해당 깃헙 이슈](https://github.com/appium/python-client/issues/955)



> 🙍‍ **이분 왈 : *So if I add a simple pause for 1 sec in implementation, it will works**
   → pause() 넣어야지만 동작함 ㅇㅇ

저 사람이 짠대로 코드를 넣으면 동작함!!!!!



```python
### 드래그앤 드랍 구현
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.by import By


# 드래그하고 드랍할 요소를 찾기 (예: accessibility_id를 사용하여 요소 찾기)
draggable = driver.find_element(By.ID, "draggable_id")
droppable = driver.find_element(By.ID, "droppable_id")

# ActionChains를 사용하여 롱프레스 동작 수행
actions = ActionChains(driver)
actions.click_and_hold(draggable).pause(1).move_to_element(droppable).perform()  # 요소를 클릭한 채로 드래그 한 후 드랍
```


>🤷‍♂️ KazuCocoa(appium 개발자 왈) :
> *Not for this issue itself, but I wondered if such action commands in Pythion lib should have been just example implementation of w3c actions as a reference.*


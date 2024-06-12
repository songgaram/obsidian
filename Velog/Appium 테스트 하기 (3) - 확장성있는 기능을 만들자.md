> _빡센 하드코딩의 흔적........._

![](https://velog.velcdn.com/images/songgaram/post/10a3ecfc-39ab-42f2-8f7f-4010328b88b1/image.png)


때는 2023년 12월.. 여러 항목을 탭하는 테스트 코드를 작성하던 나..

appium에서 제공해주는 클릭 메소드는 id 하나하나에 접근해 실행시킨다. 

하지만 이젠 하드코딩은 그만하고 싶어.. 아이디를 찾는 것 까진 오케이 > 근데 비슷한 아이디의 형태가 주어진다면 혹은 아이디에 공통적인 규칙이 있다면 해당 아이디를 모두 조회해서 리스트로 뽑을 순 없을 까?🤔

<br>

## 그래서 찾은게 iOS Predicate

>**predicate**란 fetch 혹은 메모리 내에서 어떤 값을 가져올때 메모리의 filter에 대한 제약 조건

appium에서도 [IOS_PREDICATE](https://github.com/appium/appium/blob/1.19/docs/en/writing-running-appium/ios/ios-predicate.md) 라는 메소드로 해당 기능 제공한다. 즉 어떤 값을 가져올 때 조건을 걸 수 있다는 얘기!

[[참고] 애플 공식문서](https://developer.apple.com/documentation/foundation/nspredicate)



### iOS Predicate 활용 예시
나의 경우 데이터에 따라 계속해서 id나 name값이 변하는 element가 있었다. 이를 partical하게 조회하기 위한 방법을 찾던 중 iOS Predicate를 알게되었다.


예를 들어 내가 찾고자 하는 항목의 id값이 데이터의 개수에 따라서 변한다면

```
len(data) = 5 # 데이터의 길이
element ID: 'element_id_5' # 데이터 ID

len(data) = 3
element ID: 'element_id_3'
```


해당 id의 공통 부분은 `element_id` 이며 이를 통해 유니크한 id값 하나가 아닐지라도 iOS Predicate를 이용해 해당 항목에 접근할 수 있다.

```python
#  iOS Predicate 사용
element = driver.find_element(by=AppiumBy.IOS_PREDICATE, value="name CONTAINS 'element_id_'")
```




## 반복되는 코드 iOS Predicate를 사용해 모듈화 시키기

해당 메소드 기능을 활용해 반복되는 부분을 리팩토링하자!
```python

 # 카테고리 name으로 조회 후 체크
    def category_check_by_name(driver, name):
        # name에는 선택하고자 하는 카테고리에 공통으로 들어갈 string을 입력함
        # iOS Predicate 사용해 name을 기준으로 찾은 list를 found_category 변수에 저장함
        try:
            found_category = driver.find_elements(
                by=AppiumBy.IOS_PREDICATE, value=f"name CONTAINS '{name}'")

            print("카테고리 체크 실행")
            for i in range(len(found_category)):
                found_category[i].click()
        except Exception as e:
            print("카테고리 체크 중 문제 발생 :", e)
        
```


![](https://velog.velcdn.com/images/songgaram/post/6f0c143a-0e53-4db5-a6ff-5b3468f2fdd1/image.gif)

`category_check_by_name` 함수를 사용해서 이제 더는 id를 일일히 찾아내서 무차별 클릭을 하지 않아도 되었다!!

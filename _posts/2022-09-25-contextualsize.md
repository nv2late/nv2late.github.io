---
title: "[iOS] SwipeAction 구현하기"
date: 2022-09-25
categories: [Study, iOS, Swift]
tags: [iOS, Swift, Study]
---

## 1. 구현하고자했던 화면

![스크린샷 2022-09-26 오전 12.44.07](https://user-images.githubusercontent.com/109815324/192590989-1fed504a-ec9a-43f3-90f2-e7565a36f2d0.png)

토이프로젝트의 테이블 셀의 수정 / 삭제 기능은 swipe action이었다. swipe action이라고 부르는 것도 검색해서 알았고 기본적으로 들어가는 함수 자체는 여러 설명과 공식문서를 읽어보니 어렵진 않았는데 두 가지 문제에 봉착했다.

첫째, 눈을 씻고 찾아봐도 이미지의 색을 변경하는 코드가 공식에 존재하지 않았다. 스택오버플로우부터 구글이 제안해준 3페이지까지 쭉 훑어보고 적용해봤으나 전부 먹히질 않았다.

둘째, 설상가상으로 일단 적용시키고 실행해서 스와이프를 해보니까 이미지의 앞 부분 여백이 보기에 눈쌀이 찌푸려질 정도로 예쁘지 않았다.

대략 3-4시간 걸려서 데이터연결을 제외한 나머지 기능을 원하는대로 구현했고 기록으로 남겨둘 필요를 느껴서 포스팅한다.

⚠️ 주의사항: 검색하면서 공부하고 프로젝트에 적용한 내용을 기록용도로 정리했기때문에 오류나 설명에 틀린 점이 있을 수 있습니다 ⚠️



## 2. 구현하기

구현에 필요한 메서드의 구조 자체는 오히려 심플했다.

UITableViewDelegate에다가

-   `leadingSwipeActionsConfigurationForRowAt`
-   `trailingSwipeActionsConfigurationForRowAt`

두 가지 메서드를 이용해서 테이블셀의 앞부분과 뒷부분에 버튼을 심을 수 있다. iOS11 이전 방법은 조금 더 복잡한데, 토이프로젝트 타겟의 최소를 iOS14로 잡았기때문에 굳이 이전 방식을 고집할 이유가 없었다.

```swift
func tableView(_ tableView: UITableView, leadingSwipeActionsConfigurationForRowAt indexPath: IndexPath) -> UISwipeActionsConfiguration? {
    let editAction = UIContextualAction(style: .normal, title: nil) { action, view, completion in
        self.alertForEdit(indexPath: indexPath)                                                 
        let mainEditController = UIActivityViewController(activityItems: [dateText], applicationActivities: nil)
        self.present(mainEditController, animated: true, completion: nil)
        completion(true)
    }
    // UIContextualAction 사이즈 및 색상 설정
    editAction.image = UIGraphicsImageRenderer(size: CGSize(width: 50, height: 50)).image(actions: { _ in
        UIImage(systemName: "pencil.circle.fill")?.draw(in: CGRect(x: 10, y: 0, width: 40, height: 40))
    }).withTintColor(UIColor(named: "boldColor")!)
    editAction.backgroundColor = UIColor(named: "listBgColor")
        
    let config = UISwipeActionsConfiguration(actions: [editAction])
    config.performsFirstActionWithFullSwipe = false // 스와이프시 끝까지 스와이프 불가능하게 설정
    return config
}
```

코드의 기본적인 틀은 읽어본 코드 중에 제일 깔끔한 편이었어서 [이곳](https://hasensprung.tistory.com/m/95)에서 참고했다.

Swipe Action은 UISwipeActionsConfiguration과 UIContextualAction로 구현할 수 있다.

먼저, `UISwipeActionsConfiguration` 메서드를 UITableViewDelegate에 작성한다. 그리고나서 configurationa 내부에 `UIContextualAction`을 상수로 선언한다. UIContextualAction은 테이블뷰의 열을 스와이프하면 보여지는 액션을 정의하며, 파라미터로 style, title, handler를 갖는다.

-   style은 `normal`과 `destructive` 값을 갖고 있다. normal의 경우, 유저가 스와이프를 하면 스와이프한 방향으로 끝까지 밀리지 않고 끝부분에 고정된다. destructive의 경우, normal처럼 끝까지 반대방향으로 스와이프할 경우에 테이블의 열 끝까지 밀리면서 실행된다.

    >   destructive에 대한 공식문서 설명  
    >   "An action that deletes data or performs some type of destructive task."

-   title은 액션버튼에 표시할 타이틀의 내용을 적는다.

-   handler는 액션을 선택할 때 호출할 handler block으로 action, view, completionHandler를 파라미터로 갖는다. 

변수에 담은 action의 appearance 구성은 클로저 바깥에서 작성한다.

![스크린샷 2022-09-26 오전 1.00.29](https://user-images.githubusercontent.com/109815324/192591002-0c13bc0e-cafa-4346-b1c5-995ce0659611.png)

-   image: icon(SFSymbol)이나 이미지를 설정
-   title: 액션 버튼의 타이틀 표시 설정
-   backgroundColor: 액션 버튼의 배경색상 설정



## 3. 문제해결하기: 도대체 appearance를 왜 이것만 해놓은건지

거의 3시간 가까이 매달렸다. 아무리 검색을 해봐도 명확한 답변이 없었고, 거의 꼼수에 가까운 답변들만 있었는데 대부분이 내가 아이콘 색상 하나 바꾸겠다고 저렇게까지 해야해? 라는 생각이 들 정도의 내용이 많았다. 심지어 애플포럼쪽에도 이건 애플이 이렇게 설계를 해둔거라 방법이 없다는 답변까지 있었다.

공식문서대로라면 스타일을 꾸며줄 수 있는 방법이 backgroundColor랑 이미지를 변경하거나 버튼 내부의 글자를 변경하는 정도밖에 없었다. 상단의 토이프로젝트 테이블셀 이미지를 다시 상기하자면, 해당 액션버튼의 배경색은 테이블뷰의 배경색과 동일하고 수정버튼은 강조색상으로 지정한 녹색, 삭제버튼은 붉은색으로 변경해야했다.

색상만 문제는 아니었다. 기본 사이즈로 적용했더니 아이콘이 너무 작아서 HIG 가이드라인에도 맞지 않고 조화롭지 못했다. 그리고 스와이프를 한 방향으로 여백이 너무 많아서 이것저것 뜯어보다가 뜬금없이 설마 오토레이아웃 여백문제인가? 해서 테이블셀쪽 오토레이아웃에서 leading과 trailing을 32를 줬던걸 16으로 줄여주니 보기좋게 아이콘 양옆으로 균등한 여백이 자리 잡혔다. 아무래도 디자인상 양쪽 끝 여백이 조금 널널한 편인데 스와이프를 하면 그 여백만큼 공백이 생기는 듯했다. 왜 여백이 곱절이 되는지 너무 궁금했는데 시간을 뺏겨도 한참 뺏길것 같아서 메모해두고 넘겼다.

[Setting up UIContextualAction size](https://stackoverflow.com/questions/48672723/setting-up-uicontextualaction-size), 해결책은 이 글의 답변에서 찾았다.

```swift
deleteAction.image = UIGraphicsImageRenderer(size: CGSize(width: 50, height: 50)).image(actions: { _ in
    UIImage(systemName: "trash.circle.fill")?.draw(in: CGRect(x: 0, y: 0, width: 40, height: 40))
}).withTintColor(.systemRed)
```

`UIGraphicsImageRenderer`로 액션버튼의 그래픽사이즈를 맞춘다.

```swift
func image(actions: (UIGraphicsImageRendererContext) -> Void) -> UIImage
```

위 메서드를 이용해서 버튼에 넣을 이미지와 이미지의 사이즈를 draw메서드로 같이 그려줬다. 여백도 아이콘의 사이즈도 원하는 만큼 딱 알맞게 셀의 높이랑 어우러지게 설정이 끝났다. 남은건 색상문제였다. draw메서드를 붙였다면 `.withTintColor`도 충분히 사용이 가능할 것 같아서 바로 뒤에 작성했고 실행한 뒤에 원하는 결과를 얻었다.



## 4. 추가사항

`.systemBackground`가 적용되어있거나 배경색이 `.clear`상태일 경우에 스와이프액션이 적용된 VC에서 화면전환이 발생할 경우 스와이프되는 넓이 만큼 전체 뷰가 걸쳤다가 넘어가는 현상이 눈에 보였다. 배경색을 전부 지정하면 보이지 않긴하지만 무슨 원리로 저렇게 걸쳤다가 사라지는건지 희안했다. swipeaction 관련으로 검색하면서 스와이프 한 만큼 뷰 전체가 움직인다는 식의 비슷한 현상이 있어서 이렇게 해결했다! 란 블로그 글을 본것 같은데 도무지 다시 찾을 수가 없어서 다음기회에...



## 4. 느낀점

애플이 개선을 해줘야하지 않을까 싶은데 iOS11때부터 나온 기술을 여지껏 그냥 내버려두는걸 보면 그냥 현재 방식으로 써라 이런소리일까ㅋㅋ 아니 근데 최소한 배경색을 바꿀 수 있게 프로퍼티를 마련해놨으면 글자색상 변경도 기본값으로 주는게 상식아닙니까 무슨 생각인지 알 수가 없어요😦;





## 참고링크

[[UITableView] SwipeAction - cell 삭제 (확인 Alert와 함께)](https://hasensprung.tistory.com/m/95)  
[Setting up UIContextualAction size](https://stackoverflow.com/questions/48672723/setting-up-uicontextualaction-size)  
[공식문서: UIContextualAction](https://developer.apple.com/documentation/uikit/uicontextualaction/)  
[UITableViewCell에 커스텀 Swipe action 적용하기 | Swift](https://velog.io/@dlskawns96/UITableViewCell%EC%97%90-%EC%BB%A4%EC%8A%A4%ED%85%80-Swipe-action-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0-Swift)
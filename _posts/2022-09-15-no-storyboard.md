---
title: "[iOS] 스토리보드없이 코드로 UI구현하기"
date: 2022-09-15
categories: [Study, iOS]
tags: [iOS, Swift]
---

스토리보드 없이 코드로만 UI를 구현하기 위한 방법정리

자주 잊어버리는 저를 위한 셀프 치팅시트입니다 🤧



## SceneDelegate가 화면을 담당할 경우(iOS 13 이상)

_iOS13 이상은 AppDelegate의 UI Lifecycle 역할을 SceneDelegate가 맡아서 화면과 관련된 부분을 담당_

1.   불필요한 파일 삭제

     -   Main.storyboard 파일 삭제
     -   info.plist > Scene configuration > Item 0 > Storyboard Name 삭제
     -   cmd + shift + f > main 검색 > Build Settings > Basic > Info.plist Values > UIKit Main Storyboard File Base Name 삭제

2.   SceneDelegate.swift 수정

     ```swift
     var window: UIWindow?
     
     func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
         guard let windowScene = (scene as? UIWindowScene) else { return }
         // 앱이 로드될 때 열리는 첫 번째 화면의 Scene으로 초기화
         window = UIWindow(frame: windowScene.coordinateSpace.bounds)
         window?.windowScene = windowScene
         // 어플리케이션의 시작점
         window?.rootViewController = MainViewController()
         window?.makeKeyAndVisible()
     }
     ```



## AppDelegate로 화면을 전담시킬 경우

1.   불필요한 파일 삭제

     -   SceneDelegate.swift / Main.storyboard 삭제
     -   shift + cmd + f 'main' 검색 > UIKit Main storyboard file base name 클릭 후 delete

2.   Info.plist 수정

     -   Info.plist > Information Property List 클릭 > Delete  
         삭제하는 이유: 정보 목록에서 스토리보드에 대한 참조를 제거

3.   AppDelegate 수정

     ```swift
     // window 참조 변수
     var window: UIWindow?
     
     func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
     
         window = UIWindow(frame: UIScreen.main.bounds)
         window?.makeKeyAndVisible()
         window?.backgroundColor = .systemBackground
         window?.rootViewController = ViewController()
     
         return true
     }
     ```

     

>   **참고**
>
>   **A. SceneDelegate**
>
>   유튜브 영상 - [Building Twitter using Swift5, UIKit and Google's Firebase Part 1: 1분40초 - 5분12초](https://www.youtube.com/watch?v=lGDCGja79L8)
>
>   블로그 - [[iOS][Swift] - 스토리보드 없이 코드로만 UI구현하기](https://velog.io/@lina0322/iOSSwift-%EC%8A%A4%ED%86%A0%EB%A6%AC%EB%B3%B4%EB%93%9C-%EC%97%86%EC%9D%B4-%EC%BD%94%EB%93%9C%EB%A1%9C%EB%A7%8C-UI-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0-SceneDelegate%EC%97%90%EC%84%9C-window%EC%84%A4%EC%A0%95)
>
>   **B. AppDelegate**
>
>   강의 - [The Swift Arcade Professional iOS Development Course - UIKit: Section4 - 14강](https://www.udemy.com/course/the-swift-arcade-professional-ios-development-course-uikit/)

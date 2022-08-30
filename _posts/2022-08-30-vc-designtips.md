---
title: [iOS] View Controller Programming Guide for iOS - Overview/Design Tips
date: 2022-08-30
categories: [iOS]
tags: [study, iOS, 번역, ViewController]
---

_개인적으로 두고두고 보고 읽으려고 번역해서 포스팅해둡니다. 오역, 의역 난무 주의._



### 가능하다면 시스템이 제공하는 VC를 사용해라

커스텀VC를 만들기 전에 프레임워크를 살펴보고 수행할 작업에 해당되는 VC가 이미 존재하는지 확인

-   UIKit 프레임워크는 알람을 표시하고, 사진과 비디오를 찍고, iCloud에서 파일을 관리하기 위한 VC를 제공한다. UIKit는 또한 컨텐츠를 구성하는데 사용하는 많은 standard container vc를 정의한다.
-   GameKit 프레임워크는 플레이어 매칭과 리더보드, 업적 및 기타 게임 기능을 관리하기 위한 VC를 제공한다.
-   주소록UI 프레임워크는 연락처 정보를 표시하고 선택하기 위한 VC를 제공한다.
-   MediaPlayer 프레임워크는 비디오의 재생과 관리, 유저의 라이브러리에서 미디어 에셋을 선택할 수 있는 VC를 제공한다.
-   EventKit UI 프레임워크는 유저의 캘린더 데이터를 표시하고 편집하기 위한 VC를 제공한다.
-   GLKit 프레임워크는 OpenGL 렌더링 surface를 관리하기 위한 VC를 제공한다.

-   Multipeer Connectivity 프레임워크는 다른 유저를 연결할 수 있도록 감지하고 초대하기 위한 VC를 제공한다.
-   메시지 UI 프레임워크는 이메일과 SMS메시지를 작성하기 위한 VC를 제공한다.
-   PassKit 프레임워크는 Passes를 표시하고 passbook에 추가하기 위한 VC를 제공한다.
-   소셜 프레임워크는 트위터, 페이스북과 같은 다른 미디어 사이트들의 메시지를 작성하기 위한 VC를 제공한다.
-   AVFoundation 프레임워크는 미디어 에셋을 표시하기 위한 VC를 제공한다.

시스템에서 제공하는 VC의 하이라키 구조는 절대 수정X, 변경 시 버그나 VC 동작에 문제가 생길 수 있다.



### 각 VC를 섬처럼 만들어라

VC는 항상 독립적인 객체여야 한다.
`View controllers should always be self-contained objects.`

`delegation design pattern`은 VC간의 커뮤니케이션을 관리하는데 사용된다. 델리게이션 객체의 정확한 타입은 중요하지 않다. 델리게이션 객체가 프로토콜의 메서드를 구현한다는 점이 중요하다.



### Root View를 다른 View들의 Container로만 사용해라

`Root View`를 컨테이너로 사용하면 모든 뷰에 공통되는 상위의 뷰가 제공되므로 레이아웃 작업이 간단해진다. 많은 오토 레이아웃의 constraints는 뷰를 올바르게 배치하기 위해서 공통의 `parent view`가 필요하다.



### 데이터가 어디에 있는지 알아야 한다

`model-view-controller` 디자인 설계 패턴에서 VC의 역할은 모델 객체와 뷰 객체 간의 데이터 이동을 용이하게 하는 것이다. VC는 임시 변수들에 데이터를 저장하고 일부분을 검증할 수 있지만, 주된 책임은 뷰에 정확한 정보가 포함되었는지를 확인하는 것이다. 데이터 객체는 실제 데이터를 관리하고 해당 데이터의 전반적인 무결점을 보장해야한다.

데이터와 인터페이스 분리의 예시 = `UIDocument`, `UIViewController`. 둘 사이에는 기본 관계가 존재하지 않는다.

`UIDocument` 객체는 `UIViewController`객체가 화면 상의 뷰 표시를 조정하는 동안에 데이터의 로딩과 저장을 조정한다. 만일 두 객체 사이의 관계를 만들 경우에 VC는 효율성을 위해서 문서의 정보만을 캐시해야한다는 점을 기억하자. 실제 데이터는 여전히 `문서 객체(Document object)`에 속한다.



### 변화에 적응하자

앱들은 다양한 iOS 기기에서 실행되며 VC들은 각 장치의 화면크기에 적응하도록 설계되었다. 별도의 VC를 사용하여 다른 사이즈의 화면을 가진 콘텐츠를 관리하는 대신에, 내장된 adaptivity 지원을 이용해서 VC가 사이즈별로 클래스를 변경하도록 사용해라. UIKit가 보낸 알림은 VC 코드의 변경없이도 사용자 인터페이스를 large-scale과 small-scale로 변경할 수 있는 기회를 제공할 것이다.

`adaptivity changes`에 대한 정보는 `The Adaptive Model`을 확인하라.

출처: [View Controller Programming Guide for iOS - Overview/Design Tips](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/DesignTips.html#//apple_ref/doc/uid/TP40007457-CH5-SW1)
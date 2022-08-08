---
title: "[iOS] Constraint 매개변수"
date: 2022-08-07
categories: [iOS, Swift]
tags: [study, iOS, AutoLayout]
---



## 코드로 레이아웃을 잡을때 `constraint` 함수의 매개변수 간략한 정리

전부를 넣긴 힘드니 자주 본 것 / 사용한 것들 위주로만 간단하게 정리한다.



-   `equalTo`: 아이템의 속성을 다른 아이템과 동일한 매개변수로 정의하는 제약을 반환

-   `equalTo, constant`: 매개변수로 들어오는 부분과 constant만큼 떨어진 곳에 constraint를 반환

-   `equalToConstant`: 매개변수에 height, width 를 넣어서 크기를 정한다

<hr>

-   `equalToSystemSpacingAfter` | `equalToSystemSapcingAfter: NSLayoutX or YAxisAnchor, multiplier:CGFloat`

    : 현재 앵커가 명시한 앵커에 얼마만큼(의 거리를) 따라붙을지 설정

    : X축과 Y축에 앵커에 따른 설정도 각각 가능하고, greaterThan || lessThan도 가능

    >   Returns a constraint that defines by how much the current anchor trails the specified anchor. [원문]

-   `equalToSystemSpacingBelow`: 현재 앵커가 명시한 앵커 아래에 얼만큼 떨어져 있을 것인지를 설정

    : 축 및 greater, less 위와 동일

<hr>

-   `euqalThanOrEqualTo`: 아이템의 속성을 다른 아이템보다 크거나 같게 설정
-   `lessthanOrEqualTo`: 위와 반대
-   `greaterThanOrEqualTo, constant: CGFloat`: 아이템의 속성을 다른 아이템의 속성과 상수에 지정한 오프셋보다 크거나 같은 것으로 정의
-   `lessthanOrEqualTo, constant: CGFloat`: 위와 반대





>   **출처**
>
>   [애플 도큐먼트](https://developer.apple.com/documentation/uikit/nslayoutdimension)
>
>   -   https://developer.apple.com/documentation/uikit/nslayoutxaxisanchor
>   -   https://developer.apple.com/documentation/uikit/nslayoutyaxisanchor
>
>   [AutoLayout를 잡아보자](https://velog.io/@yoonah-dev/AutoLayout를-잡아보자)
---
layout: post
title: Warning 제거하기
tags: [Unity, Editor]
image: /assets/img/post/2019-08-23-WarningRemoval/Title.png
description: >
  유니티의 불필요한 경고를 제거하는 방법에 대해 알아봅시다.
---

* toc
{:toc .large-only}

## Warning 폭탄

아마 대부분의 개발자들이 Unity에서 작업할 때 가장 신경 쓰이는 것 중 하나는 바로 Console 창을 가득 매운 노란 메세지들 일 것 입니다. 

![Warnings](/assets/img/post/2019-08-23-WarningRemoval/Warnings.png "Warnings")

Warning이 나오는 이유는 다양합니다. 초기값을 지정해주지 않아서 일 수도 있고, 외부 어셋을 사용할 때는 이제는 Obsolete거나 Deprecated 될 함수들에 의해서 생기기도 합니다. 

## 글로벌 커스텀 전처리 지시어

이러한 Warning없이 깔끔한 콘솔창, 내가 원하는 로그기록만 볼 수 있는 콘솔창을 만들어봅시다. 

모든 코드를 일일이 고치지 않아도, 모든 변수에 의미없는 null 초기값을 넣지 않아도 해결할 수 있습니다. 비밀은 바로 csc.rsp<sup id="a1">[[1]](#f1)</sup>파일에 있습니다. 

csc.rsp 파일은 글로벌 커스텀 #define 기능을 수행합니다. 무슨 말이냐면, 플레이어 스크립트와 에디터 스크립트 전체에 걸쳐서 전처릴 지시어를 정의하여서 컴파일 한다는 것입니다. 

예를 들면 csc.rsp 파일에 `-define:A` 를 넣는다면, 모든 코드에 `#define A` 라는 전처리 지시어가 정의가 된다는 것입니다. 

## Warning 제거하기

이를 활용해서 우리가 가장 쉽게 만나볼 수 있는 <code>Field '<b>variable</b>' is never assigned to, and will always have its default value</code> 오류를 없애보도록 합시다. 

![Sample Warning](/assets/img/post/2019-08-23-WarningRemoval/SampleWarning.png "Sample Warning")

경고 메세지를 보면, 해당 경고의 코드를 알 수 있습니다. 위의 경우는 CS0649, 즉 0649 코드의 경고인 것입니다. 

그렇다면 우리는 csc.rsp 파일에 `-nowarn:0649`을 넣어줍니다. 

![Csc](/assets/img/post/2019-08-23-WarningRemoval/csc.png "CSC File")

이는 해당 경고 메세지를 모두 무시해줍니다! 

![Clean Console](/assets/img/post/2019-08-23-WarningRemoval/CleanConsole.png "Clean Console")

야호! 이제 우리는 깨끗한 콘솔창을 볼 수 있습니다. 

이와 같은 방법으로 `-nowarn:0618`을 통해 Obsolete 경고를, `-nowarn:0414`를 통해 Never Used Value 경고를 제거할 수 있습니다. 

***

<b id="f1">[1]</b> `.NET 4.x Eqivalent` 스크립팅 런타임 버전 컴파일러 한정입니다. `.NET 3.5 Equivalent` 스크립팅 런타임 버전을 대상으로 하는 경우 mcs.rsp 파일을 사용합니다. [[↩]](#a1)

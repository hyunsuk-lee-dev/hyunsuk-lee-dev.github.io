---
layout: post
title: '코루틴에 대해서-2'
tags: [태그]
image : /assets/img/post/2021-05-26-Coroutines-2/main.jpg
description: >
  블로그 포스트의 템플릿에 대한 자료입니다.
---

* toc
{:toc .large-only}
# 코루틴에 대해선 2편

지난번 시작, 중지, 재시작 등 기본 기능에 대해서 알아봤다면, 이번에는 중첩해서 동작하는 코루틴에 대해서 알아보도록 하겠습니다.



## 중첩 코루틴

### 1. 코루틴은 중첩이 가능합니다

```c#
private IEnumerator OuterCoroutine()
{
    CustomDebug.Log(debugTag, "This is Outer-Coroutine.");
    yield return StartCoroutine(innerIEnumerator);
    yield return waitOneSecondInstruction;
    CustomDebug.Log(debugTag, "Outer-Coroutine is completed!");
}

private IEnumerator InnerCoroutine()
{
    CustomDebug.Log(debugTag, "This is Inner-Coroutine.");
    yield return waitThreeSecondsInstruction;
    CustomDebug.Log(debugTag, "Inner-Coroutine is completed!");
} 
```

![NestedCoroutine](/assets/img/post/2021-05-26-Coroutines-2/NestedCoroutine.png){:.center}



### 2. 중첩된 코루틴의 외부 코루틴을 정지시키면 내부 코루틴도 같이 멈출까요?

``` c#
private IEnumerator OuterCoroutineStop()
{
    CustomDebug.Log(debugTag, "Start Outer-Coroutine");
    StartCoroutine(outerIEnumerator);
    yield return waitOneSecondInstruction;
    CustomDebug.Log(debugTag, "Stop Outer-Coroutine");
    StopCoroutine(outerIEnumerator);
}
```

![NestedParentStop](/assets/img/post/2021-05-26-Coroutines-2/NestedStopOuter.png){:.center}

아니다! 외부 코루틴이 멈춰도, 내부 코루틴은 멈추지 않는다. `Coroutine`을 사용한 방법도 동일한 결과를 낸다.



### 3. 중첩된 코루틴의 내부 코루틴을 정지시키면 외부 코루틴은 넘어갈까요?

``` c#
private IEnumerator InnerCoroutineStop()
{
    CustomDebug.Log(debugTag, "Start Outer-Coroutine");
    StartCoroutine(outerIEnumerator);
    yield return waitOneSecondInstruction;

    CustomDebug.Log(debugTag, "Stop Inner-Coroutine");
    StopCoroutine(innerIEnumerator);
}
```

![NestedParentStop](/assets/img/post/2021-05-26-Coroutines-2/NestedStopInner.png){:.center}

아니다! 외부 코루틴은 영원히 내부 코루틴이 마치기를 기다린다. 



### 4. 중첩된 코루틴의 내부 코루틴을 다시 시작하면 외부 코루틴은 멈출 수 있을까요?

```c#
private IEnumerator RestartInnerCoroutine()
{
    CustomDebug.Log(debugTag, "Start Outer-Coroutine");
    StartCoroutine(outerIEnumerator);
    yield return waitOneSecondInstruction;

    CustomDebug.Log(debugTag, "Stop Inner-Coroutine");
    StopCoroutine(innerIEnumerator);
    yield return waitOneSecondInstruction;

    CustomDebug.Log(debugTag, "Restart Inner-Coroutine");
    // 두 방법 모두 결과는 동일했다.
    //innerIEnumerator = InnerCoroutine();
    StartCoroutine(innerIEnumerator);
}
```

![NestedRestartInner](/assets/img/post/2021-05-26-Coroutines-2/NestedRestartInner.png){:.center}

아니다. 내부 코루틴이 다시 재시작되고, 끝을 마쳐도 외부 코루틴은 끝나지 않았다. 




---



이미지는 아래와 같이 첨부합니다.

![이미지](/assets/img/post/New Unity.jpg){:.center}

이것은 이미지 캡션입니다.
{:.figcaption}




이미지 사이즈를 특정하고 싶다면 아래와 같이 사용합니다.

![이미지](/assets/img/post/New Unity.jpg){:.center width="200px"}



링크는 아래와 같이 넣습니다.

[링크될 텍스트]("https://google.com"){:target="_blank"}




> 인용 사용

> 인용과 강조 동시에 사용
{:.em .lead}

강조 사용
{:.lead}




아래는 수평선입니다.

---
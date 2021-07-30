---
layout: post
title: '코루틴에 대해서-2'
tags: [Unity]
image : /assets/img/post/Unity2021Wide.png
description: >
  유니티 코루틴의 심화활용 방법 중 하나인 중첩 코루틴에 대해서 알아봅시다.
---

* toc
{:toc .large-only}
# 코루틴에 대해선 2편

[지난번 시작, 중지, 재시작 등 기본 기능에 대해서](https://leehs27.github.io/programming/2021-05-25-Coroutines-1/){:target="_blank"} 알아봤다면, 이번에는 중첩해서 동작하는 코루틴에 대해서 알아보도록 하겠습니다.

## 중첩 코루틴

코루틴은 자체적으로 다른 코루틴을 부를 수 있습니다. 추가로, 불러온 코루틴이 멈출 때까지 기다릴 수도 있죠. 

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

![NestedCoroutine](/assets/img/post/2021-07-30-Coroutines-2/NestedCoroutine.png){:.center}

## 헷갈리는 중첩 코루틴

중첩 코루틴을 쓰다보면 헷갈리는 케이스들이 몇가지 있습니다. 그래서 그런 케이스들을 정리해두면 나중에 중첩 코루틴을 사용할 때, 의도치않은 결과를 내는 것을 방지할 수 있을 것입니다.

### 1. 중첩된 코루틴의 외부 코루틴을 정지시키면 내부 코루틴도 같이 멈출까요?

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

![NestedParentStop](/assets/img/post/2021-07-30-Coroutines-2/NestedStopOuter.png){:.center}

아닙니다! 외부 코루틴이 멈춰도, 내부 코루틴은 멈추지 않습니다.<sub> `Coroutine`을 사용한 방법도 동일한 결과를 냅니다. </sub>

### 2. 중첩된 코루틴의 내부 코루틴을 정지시키면 외부 코루틴은 넘어갈까요?

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

![NestedParentStop](/assets/img/post/2021-07-30-Coroutines-2/NestedStopInner.png){:.center}

아닙니다! 외부 코루틴은 영원히 내부 코루틴이 마치기를 기다립니다. 

### 3. 중첩된 코루틴의 내부 코루틴을 다시 시작하면 외부 코루틴은 끝날 수 있을까요?

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

![NestedRestartInner](/assets/img/post/2021-07-30-Coroutines-2/NestedRestartInner.png){:.center}

아닙니다! 내부 코루틴이 다시 재시작되고, 끝을 마쳐도 외부 코루틴은 끝나지 않았다. 
내부 코루틴을 일시정지했다가 멈춘 부분부터 다시 시작해도, 아예 내부 코루틴을 초기화해서 다시 시작해도 외부 코루틴은 끝나지 못하고 계속해서 내부코루틴이 끝나기를 기다렸습니다.

## 정리

- 코루틴에서 다른 코루틴을 부를 수 있다. 이를 중첩 코루틴이라 부르며 `yield return StartCoroutine()`을 통해 시작한 코루틴을 기다릴 수도 있다.
- 외부 코루틴을 정지시켜도 내부 코루틴은 정지되지 않는다.
- 내부 코루틴을 정지시키면 외부 코루틴은 영원히 기다린다.
- 내부 코루틴을 다시 시작해도 외부 코루틴은 영원히 기다린다.


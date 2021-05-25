---
layout: post
title: '코루틴에 대해서-1'
tags: [Unity]
image : /assets/img/post/New Unity.jpg
description: >
  유니티에서 자주 사용하는 기능인 코루틴에 대해서 알아봅시다.
---

* toc
{:toc .large-only}


# 코루틴에 대해서 1편

아마 유니티를 개발하면서 코루틴에 대해서 모르는 사람은 극히 드물 것입니다. 그러므로 코루틴이 무엇인지에 대한 내용은 차치하고 코루틴을 쓸 때 애매하고 헷갈리던 것들을 위주로 알아보도록 하겠습니다. 

## 1. 유니티의 기본 메세지인 Start 함수는 코루틴으로 변경이 가능합니다.

```C#
private IEnumerator Start()
{
    CustomDebug.Log(debugTag, "This is Start-coroutine. Start-coroutine starts automatically");
    yield return waitOneSecondInstruction;
    CustomDebug.Log(debugTag, "Start-coroutine is completed!");
}
```

![Start-Coroutine](/assets/img/post/2021-05-25-Coroutines-1/Start-Coroutine.png){:center}

Start Coroutine 결과
{:.figcaption}

Play를 누르면 해당 코루틴이 자동으로 실행됩니다.

> 이 트릭은 OnCollision-, OnTrigger- 함수에도 동일하게 적용됩니다. 다만, 그 외의 다른 함수는 안됩니다..
>
> ```c#
> private IEnumerator Awake()
> {
>        CustomDebug.Log(debugTag, "This is Awake-coroutine. Awake-coroutine >starts automatically");
>        yield return waitOneSecondInstruction;
>        CustomDebug.Log(debugTag, "Awake-coroutine is completed!");
> }
> ```
>
> ![Awake-Coroutine](/assets/img/post/2021-05-25-Coroutines-1/Awake-Coroutine.png){:center}
>
> Awake는 Coroutine으로 만들면 에러가 난다. 
> {:.figcaption}

## 2. 앞으로 나올 테스트의 결과들은 아래의 샘플 코루틴을 이용한 테스트 결과입니다.

```c#
private YieldInstruction waitOneSecondInstruction = new WaitForSeconds(1f);

private IEnumerator SampleCoroutine()
{
    CustomDebug.Log(debugTag, "Sample-Coroutine is started.");
    yield return waitOneSecondInstruction;
    CustomDebug.Log(debugTag, "Sample-Coroutine goes step 1");
    yield return waitOneSecondInstruction;
    CustomDebug.Log(debugTag, "Sample-Coroutine goes step 2");
    yield return waitOneSecondInstruction;
    CustomDebug.Log(debugTag, "Sample-Coroutine is completed!");
}
```

## 3. 코루틴은 3가지 방법으로 시작이 가능합니다.

#### 1. 이름

```C#
private void StartByName()
{
    CustomDebug.Log(debugTag, "Start Sample-Coroutine by Name.");
    StartCoroutine(nameof(SampleCoroutine));
}
```

![StartByName](/assets/img/post/2021-05-25-Coroutines-1/StartByName.png){:center}


> 단, 이름을 사용하는 방법은 코루틴이 매개변수를 가지고 있지 않을 때만 가능합니다.
>
> ```c#
> public void StartByNameWithParameter()
> {
>  	CustomDebug.Log(debugTag, "Start Parameter-Coroutine by Name.");
>  	// 컴파일 에러가 난다.
>  	StartCoroutine(nameof(ParameterCoroutine("Hello World!")));
> }
> ```

#### 2. 함수

```C#
private void StartByMethod()
{
    CustomDebug.Log(debugTag, "Start Sample-Coroutine by Method.");
    StartCoroutine(SampleCoroutine());
}
```

![StartByMethod](/assets/img/post/2021-05-25-Coroutines-1/StartByMethod.png){:center}

#### 3. `IEnumerator`

```c#
private void StartByIEnumerator()
{
    IEnumerator sampleIEnumerator = SampleCoroutine();

    CustomDebug.Log(debugTag, "Start Sample-Coroutine by IEnumerator.");
    StartCoroutine(sampleIEnumerator);
}
```

![StartByIEnumerator](/assets/img/post/2021-05-25-Coroutines-1/StartByIEnumerator.png){:center}


## 4. 코루틴은 멈추는 방법도 3가지입니다.

#### 1. 이름

```c#
private IEnumerator StartAndStopByName()
{
    CustomDebug.Log(debugTag, "Start Sample-Coroutine by Name.");
    StartCoroutine(nameof(SampleCoroutine));
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Stop Sample-Coroutine by Name.");
    StopCoroutine(nameof(SampleCoroutine));
}
```

![StartAndStopByName](/assets/img/post/2021-05-25-Coroutines-1/StartAndStopByName.png){:center}

#### 2. `IEnumerator`

```c#
private IEnumerator StartAndStopByIEnumerator()
{
    IEnumerator sampleIEnumerator = SampleCoroutine();

    CustomDebug.Log(debugTag, "Start Sample-Coroutine by IEnumerator.");
    StartCoroutine(sampleIEnumerator);
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Stop Sample-Coroutine by IEnumerator.");
    StopCoroutine(sampleIEnumerator);
}
```

![StartAndStopByIEnumerator](/assets/img/post/2021-05-25-Coroutines-1/StartAndStopByIEnumerator.png){:center}

#### 3. `Coroutine` 

```c#
private IEnumerator StartAndStopByCoroutine()
{
    CustomDebug.Log(debugTag, "Start Sample-Coroutine by Coroutine.");
    // 시작 방식은 이름이나 IEnumerator등 다른 방법으로 해도 상관없다.
    Coroutine sampleCoroutine = StartCoroutine(SampleCoroutine());
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Stop Sample-Coroutine by Coroutine.");
    StopCoroutine(sampleCoroutine);
}
```

![StartAndStopByCoroutine](/assets/img/post/2021-05-25-Coroutines-1/StartAndStopByCoroutine.png){:center}

> 함수를 이용한 방법은 코루틴을 멈출 수 없습니다.
>
> ```c#
> private IEnumerator StartAndStopByMethod()
> {
>      CustomDebug.Log(debugTag, "Start Sample-Coroutine by Method.");
>      StartCoroutine(SampleCoroutine());
>      yield return waitTwoSecondsInstruction;
> 
>      CustomDebug.Log(debugTag, "Stop Sample-Coroutine by Method.");
>      // 동작하지 않는다.
>      StopCoroutine(SampleCoroutine());
> }
> ```
>
> ![StartAndStopByMethod](/assets/img/post/2021-05-25-Coroutines-1/StartAndStopByMethod.png){:center}

## 5. 코루틴을 재시작하는 2가지 방법이 있습니다.

#### 1. 이름

```c#
private IEnumerator RestartByName()
{
    CustomDebug.Log(debugTag, "Start Sample-Coroutine by Name.");
    StartCoroutine(nameof(SampleCoroutine));
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Stop Sample-Coroutine by Name.");
    StopCoroutine(nameof(SampleCoroutine));
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Restart Sample-Coroutine by Name.");
    StartCoroutine(nameof(SampleCoroutine));
}
```

![RestartByName](/assets/img/post/2021-05-25-Coroutines-1/RestartByName.png){:center}

#### 2. `IEnumerator`

```c#
private IEnumerator RestartByIEnumerator()
{
    IEnumerator sampleCoroutine = SampleCoroutine();

    CustomDebug.Log(debugTag, "Start Sample-Coroutine by IEnumerator.");
    StartCoroutine(sampleCoroutine);
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Stop Sample-Coroutine by IEnumerator.");
    StopCoroutine(sampleCoroutine);
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Restart Sample-Coroutine by IEnumerator.");
    StartCoroutine(sampleCoroutine);
}
```

![RestartByIEnumerator](/assets/img/post/2021-05-25-Coroutines-1/RestartByIEnumerator.png){:center}

> 단, 유의해야할 점은 이름을 이용한 방법은 코루틴이 처음부터 재시작이 되지만, `IEnumerator`를 통한 방법은 해당 코루틴이 멈춘 지점에서 다시 시작합니다. 엄밀히 말하면 Restart보다는 Resume의 개념에 조금 더 가깝겠습니다.


> 함수를 이용한 방법은 정지가 안되기 때문에 해당 코드를 이용하면 두 번 실행이 됩니다.
>
> ``` c#
> private IEnumerator RestartByMethod()
> {
>      CustomDebug.Log(debugTag, "Start Sample-Coroutine by Method.");
>      StartCoroutine(SampleCoroutine());
>      yield return waitTwoSecondsInstruction;
> 
>      CustomDebug.Log(debugTag, "Stop Sample-Coroutine by Method.");
>      // 동작하지 않는다.
>      StopCoroutine(SampleCoroutine());
>      yield return waitTwoSecondsInstruction;
> 
>      CustomDebug.Log(debugTag, "Restart Sample-Coroutine by Method.");
>      // 동일한 코루틴이 2개가 실행된다.
>      StartCoroutine(SampleCoroutine());
> }
> ```
>
> ![RestartByMethod](/assets/img/post/2021-05-25-Coroutines-1/RestartByMethod.png){:center}

>`Coroutine`은 `StartCoroutine`함수의 매개변수로 적합하지 않기 때문에 재시작이 불가능합니다.
>
>```c#
>private IEnumerator RestartByCoroutine()
>{
>        CustomDebug.Log(debugTag, "Start Sample-Coroutine by Coroutine.");
>        // 시작 방식은 이름이나 IEnumerator등 어떠한 방법으로 해도 상관은 없다.
>        Coroutine sampleCoroutine = StartCoroutine(SampleCoroutine());
>        yield return waitTwoSecondsInstruction;
>
>        CustomDebug.Log(debugTag, "Stop Sample-Coroutine by Coroutine.");
>        StopCoroutine(sampleCoroutine);
>        yield return waitTwoSecondsInstruction;
>
>        CustomDebug.Log(debugTag, "Restart Sample-Coroutine by Coroutine.");
>        // 컴파일 에러가 난다.
>        // StartCoroutine(sampleCoroutine);
>}
>```

## 6. 코루틴의 기본 기능에 대한 결론

|         기능         | 코루틴 시작 | 코루틴 정지 |       코루틴 재시작        |         기타         |
| :------------------: | :---------: | :---------: | :------------------------: | :------------------: |
|     이름을 이용      |    가능     |    가능     |   가능[처음부터 재시작]    | 매개변수 사용 불가능 |
|     함수를 이용      |    가능     |   불가능    |   불가능[정지가 불가능]    |                      |
| `IEnumerator`를 이용 |    가능     |    가능     | 가능[멈춘 지점에서 재시작] |                      |
|  `Coroutine`을 이용  |      -      |    가능     |   불가능[시작이 불가능]    |                      |

결과적으로,

- 매개변수를 사용할 수 없는 **이름을 이용하는 방법**은 사용하지 않는다.
- 단순히 코루틴을 **실행**만 시키고 싶을 때는 간편하게 **함수를 이용하는 방법**을 이용한다.
- 코루틴을 **정지 및 재시작**을 할 필요가 있을 때는 **`IEnumerator`**를 사용하도록 한다. 
- 그 이외의 특수한 경우에 대해서만 제한적으로 **`Coroutine`**을 이용하는 것이 가장 바람직합니다.

## *. `IEnumerator`를 사용해서 완전한 재시작을 하고 싶을 때.

`IEnumerator`를 사용한 방법의 유일한 단점(지금까지는 그렇게 보입니다.)인 "코루틴 재시작 시, 멈춘 지점이 아닌 처음부터 재시작"을 하려면 어떻게 해야할까요?
바로 `IEnumerator`를 다시 할당해주면 됩니다.

```c#
public IEnumerator CompleteRestartByIEnumerator()
{
    IEnumerator sampleIEnumerator = SampleCoroutine();

    CustomDebug.Log(debugTag, "Start Sample-Coroutine by IEnumerator.");
    StartCoroutine(sampleIEnumerator);
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Stop Sample-Coroutine by IEnumerator.");
    StopCoroutine(sampleIEnumerator);
    yield return waitTwoSecondsInstruction;

    CustomDebug.Log(debugTag, "Restart Sample-Coroutine by IEnumerator.");
    sampleIEnumerator = SampleCoroutine();
    StartCoroutine(sampleIEnumerator);
}
```

![CompleteRestartByIEnumerator](/assets/img/post/2021-05-25-Coroutines-1/CompleteRestartByIEnumerator.png){:center}

다음에는 코루틴의 중첩에 대해 알아보도록 하겠습니다.
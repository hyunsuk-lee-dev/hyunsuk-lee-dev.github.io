---
layout: post
title: 'Update를 호출할 때, 비용이 들까'
tags: [Unity]
image : /assets/img/post/New Unity.jpg
description: >
  Unity에서 Update를 호출하는데 비용이 얼마나 드는지, 다른 방식을 이용할 때와 차이가 얼마나 나는지를 확인해봅시다.
---

* toc
{:toc .large-only}

## Update문의 비용

옛날 <sub>-옛날이라고 표현하는게 맞을 것 같다.-</sub> 2015년말 즈음, 유니티 블로그에는 [10000번의 Update를 호출하는 테스트에 대한 글](https://blogs.unity3d.com/kr/2015/12/23/1k-update-calls/){:target="_blank"}이 올라왔습니다.

글의 요지는 이러합니다. 

많은 사람들이 지금처럼 IDE가 도와주지 않던 때, `Update`나 `OnEnable`, `OnDisable`과 같은 유니티 메세지 함수들은 인텔리센스가 지원되지 않으니, 이런 베이스 클래스를 만들곤 했습니다.  

```c#
public abstract class BaseMonobehaviour : MonoBehaviour 
{  
    protected virtual void Awake() {}  
    protected virtual void Start() {}
    protected virtual void OnEnable() {}
    protected virtual void OnDisable() {}
    protected virtual void Update() {}
    protected virtual void LateUpdate() {}
    protected virtual void FixedUpdate() {}
}
```

문제는 이런 클래스에서는 `Update`를 사용하지 않아도, 유니티 내부에서는 매 프레임마다 이 함수를 호출하게 됩니다. 그리고 아무것도 하지 않으니, 상관없다고 생각하겠지만, 사실은 이 메세지들을 호출하는 것만으로도 비용이 발생한다는 것이 해당 글의 내용이었습니다.

---

## 테스트

원문에서는 Update 호출의 비용을 알아보기 위해 테스트를 진행하는데, 10000개의 작업을 위해 10000개의 `Update`를 호출할 때의 시간과 1개의 `Update`를 호출할 때의 시간을 비교합니다. 

> 그래서 저도 해봤습니다.
{:.em .lead}

저는 원문의 테스트에 경우를 추가해서 총 3가지 경우를 비교했습니다.

1. 일반적인 Update Unity Message를 이용한 방식

   ```c#
   private void Update()
   {
       count++;
   }
   ```

2. Coroutine을 이용한 방식

   ```c#
   private void Start()
   {
       StartCoroutine(UpdateCoroutine());
   }
   
   private IEnumerator UpdateCoroutine()
   {
       while(true)
       {
           count++;
           yield return new WaitForEndOfFrame();
       }
   }
   ```

3. Update Manager를 이용한 방식

   ```	c#
   private CustomUpdateBehaviour[] customUpdateBehaviours;
   
   private void Update()
   {
       foreach(var customUpdateBehaviour in customUpdateBehaviours)
       {
           customUpdateBehaviour.CustomUpdate();
       }
   }
   ```

마지막으로 시간측정은 `Update` 콜과 `LateUpdate` 콜 사이의 시간을 `Stopwatch`로 측정했고, 이 측정 스크립트는 Script Execution Order을 `Default Time - 50` 으로 설정했습니다. 

```c#
private void Update()
{
    if(Time.time < startTime)
        return;
    stopwatch.Start();
}

private void LateUpdate()
{
    if(Time.time < startTime)
        return;

    stopwatch.Stop();
    updateCount++;
    frameTime = stopwatch.ElapsedTicks;
    totalTime += frameTime;
    stopwatch.Reset();
}

private IEnumerator Log()
{
    WaitForSeconds delay = new WaitForSeconds(1f);

    while(true)
    {
        yield return delay;

        if(updateCount > 0)
        {
            UnityEngine.Debug.Log("Last time: " + (float)frameTime / Stopwatch.Frequency * 1000f + "ms. " +
                                  "Average time: " + (float)(totalTime / updateCount) / Stopwatch.Frequency * 1000f + "ms.");
        }
    }
}
```

이 테스트는 OS는 윈도우10, 유니티 버전은 2020.3.3f1을 사용했고, 스크립팅 백엔드는 IL2CPP를 사용했으며, 빌드가 아닌 에디터 환경에서 실시 되었습니다. 

이 테스트는 제 [리포지토리](https://github.com/leehs27/UpdateCallsCost){:target="_blank"}를 통해 직접 실행해보실 수 있습니다. 

## 테스트 결과

결과는 다소 놀랍습니다.

1번 일반적인 Update Unity Message를 이용한 방식은 2.46ms,  
2번 Coroutine을 이용한 방식은  0.15ms,  
3번 Update Manager를 이용한 방식은 0.10ms가 나왔습니다.

| 1. 일반적인 Update 방식 | 2. Coroutine을 이용한 방식 | 3. Update Manager를 이용한 방식 |
| :---------------------: | :------------------------: | :-----------------------------: |
|         2.46ms          |           0.15ms           |             0.10ms              |

딱 봐도 Update를 이용한 방식이 비용이 훨씬 더 많이 드는 것을 알 수 있습니다. 약 20배정도나 말입니다. 

위 코드에서는 Update문에 `count++;` 라는 코드가 동작하니 오래걸리는 것 아니냐구요? 해당 구문을 주석처리하고 Updata문을 완전히 빈 상태로 놔두어도 결과는 동일했습니다. 

세상에, 비어있는 함수를 콜하는데도 비용이 들어간다니.
{:.lead}

제 Visual Studio에서는 Update가 비어있으면, `The Unity message 'Update' is empty.`라는 `UNT0001 Message`가 나오는데, 괜히 나오는게 아니었습니다.

## 결론

원문은 포스트를 이렇게 마칩니다.

> 물론 이건 당신의 프로젝트에 달렸어요. <sub>(중략)</sub> 이미 지금은 개발이 너무 많이 진행되어서 이러한 수정을 하기엔 너무 늦었을 수도 있습니다.
> 다만, 이제 당신은 데이터를 가졌고, 당신의 다음 프로젝트를 시작할 때는 이것을 한번 생각해 보세요.
{:.em .lead}

이 결과를 보고 물론 모든 오브젝트의 업데이트문을 사용하지 않을 수는 없을 것입니다.  
하지만, 적어도 사용하지 않는 Update 문이 있다면 꼭 지워주거나, Update를 가진 오브젝트를 엄청나게 많이 생성 및 관리할 일이 있다면, 매니저형식을 이용하는 것은 할 수 있지 않을까요? 

비록 10000개의 2ms 밖에 되지 않은 작은 수치지만 이런 변화와 노력들 하나하나가 프로그램을 좀 더 가볍고, 빠르게 만들 수 있지 않을까 기대해봅니다.
{:.lead}


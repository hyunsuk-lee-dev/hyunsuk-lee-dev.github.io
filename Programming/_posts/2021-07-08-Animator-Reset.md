---
layout: post
title: '유니티 애니메이터 초기화'
tags: [Unity]
image : /assets/img/post/Unity2021wide.png
description: >
  유니티의 애니메이션을 원하는 때에 초기화하는 방법에 대해 알아봅시다.
---

* toc
{:toc .large-only}
# 애니메이션 초기화

가끔 유니티에서 애니메이션을 강제로 초기화해야 할 때가 있습니다. 몬스터가 죽었다가 다시 살아날 때라든지, UI가 꺼졌다가 다시 켜질 때라든지 등등의 경우에 말입니다. 사실 엄밀히 말하면 애니메이션을 초기화하는게 아니라 애니메이터`Animator`를 초기화가 필요한 때가 있습니다.

## 오브젝트를 껐다 켠다.

기본적으로 `Animator` 컴포넌트를 가지고 있는 `GameObject`의 `Active`를 껐다 켜면 초기화가 됩니다.

```csharp
if(GUILayout.Button("SetActive(false)"))
{
    target.SetActive(false);
}
else if(GUILayout.Button("SetActive(true)"))
{
    target.SetActive(true);
}
```

![SetActive](/assets/img/post/2021-07-08-Animator-Reset/SetActive.gif){:.center}

게임 오브젝트 자체를 껐다 켰을 때.
{:.figcaption}

## 애니메이터를 껐다 켠다.

그런데 `GameObject`를 끌 수 없는 상황도 있습니다. 해당 오브젝트가 가지고 있는 또 다른 컴포넌트는 계속 동작이 되어야 할 수도 있기 때문입니다. 

그럴 때 `GameObject` 대신 `Animator`의 `enable`을 껐다 켜면 어떨까요. 

```csharp
animator = GetComponent<Animator>();

if(GUILayout.Button("enable = false"))
{
    animator.enabled = false;
}
else if (GUILayout.Button("enable = true"))
{
    animator.enabled = true;
}
```

![Enable](/assets/img/post/2021-07-08-Animator-Reset/Enable.gif){:.center}

애니메이터만 껐다 켰을 때.
{:.figcaption}

초기화는 되지 않고 중지된 부분부터 다시 실행됩니다.
{:.lead}

## Rebind를 한다.

`Animator`는 [`Rebind`라는 함수](https://docs.unity3d.com/ScriptReference/Animator.Rebind.html){:target="_blank"}를 가지고 있습니다.
이 함수를 통해 애니메이터를 꼈다 켜는 것만으로 애니메이터를 초기화시킬 수 있습니다.

```csharp
animator = GetComponent<Animator>();

if(GUILayout.Button("enable = false"))
{
    animator.enabled = false;
}
else if (GUILayout.Button("enable = true"))
{
    animator.enabled = true;
}
else if(GUILayout.Button("Rebind"))
{
    animator.Rebind();
}
```

![Rebind](/assets/img/post/2021-07-08-Animator-Reset/Rebind.gif){:.center}

Rebind를 사용했을 때.
{:.figcaption}

**Rebind**를 사용하면 초기화가 잘 되는 것을 확인할 수 있습니다.

## 결론

1. `GameObject`를 `SetActive(false);`하고 `SetActive(true);`하면 애니메이터가 초기화된다.
2. `Animator`를 `enable=false;`하고 `enable=true;`하면 애니메이터가 멈춘 시점부터 재개된다.
3. `Animator`를 `enable=false;`하고 `animator.Rebind();`하고 `enable=true;`하면 애니메이터가 초기화된다.
---
layout: post
title: ProjectOnPlane을 활용한 UI의 위치 설정
tags: [Unity, UI]
---

* toc
{:toc .large-only}

## UI의 방향은 어디여야 할까요.    

VR에서, 특히 UI쪽을 만들 때, 카메라의 방향과 UI를 일치시켜야 할 때가 종종 있습니다.  
사용자가 바라보는 방향에 UI가 등장해야, 사용자에게 인식시킬 수 있고, 사용자에게 불필요한 시선의 이동을 강요하지 않기 때문입니다.  
하지만, 사용자가 순간적으로 천장이나 바닥을 바라볼 때, 그곳에 UI가 등장한다면, 사용자는 UI를 조작하기 위해 고개를 계속 치켜들거나 고꾸라져야 하는 난감한 상황이 됩니다.  
그렇기에 UI는 사용자가 바라보는 시선의 방향과 일치하지만, 시선의 높낮이와는 무관하게 등장해야 합니다. 즉, Y축 값은 고정되어야 합니다.

## ProjectOnPlane이란,  

이러한 상황에서, 사용할 수 있는 것이 바로 Vector3 클래스의 ProjectOnPlane 입니다.  
[유니티 Document](https://docs.unity3d.com/2019.1/Documentation/ScriptReference/Vector3.ProjectOnPlane.html){:target="_blank"}의 설명은 아래와 같습니다.  

```c#
public static Vector3 ProjectOnPlane(Vector3 vector, Vector3 planeNormal);
```

#### Parameters

| **`planeNormal`** | The direction from the vector towards the plane. |
| **`vector`**      | The location of the vector above the plane.      |

#### Returns

| **`Vector3`**     | The location of the vector on the plane.         |

## 고등학교 기억을 떠올려봅시다.    

이 함수는 고등학교 수학시간에 열심히 배운 **정사영**과 같습니다. 

![Orthogonal-Projection](/assets/img/post/projection.png){:.align-center}

직선의 정사영
{:.figcaption}

`vector`는 우리가 정사영 시키고자 하는 직선 $$ A $$ 이고, `planeNormal`은 우리가 투영시킨 평면 $$ B $$의 수직인 벡터 $$ \hat{B} $$ 입니다.

그리고 그렇게 얻어진 Return 값이 정사영된 벡터인 $$ A^{\prime} $$ 입니다. 

## UI에 적용해봅시다.  

위에서 우리는 UI를 Y축의 값을 고정시키며 이동시키고자 하였고, 그렇게 하기위해서는 시선의 정면을 가리키는 벡터를 XZ평면, 곧 Y축을 Normal로 가지고 있는 평면에 정사영 시켜야하는 것입니다. 

```c#
canvas.transform.position = camera.position + 
Vector3.ProjectOnPlane( camera.forward , Vector3.up ).normalized * canvasDistance;
```

위의 코드는 Canvas가 Camera의 정면에서 나타나게 하기 위해서 실제 프로젝트에서 사용된 코드의 간소화된 버전입니다. Canvas의 위치는 "Camera의 위치 + Camera의 정면 방향 * 거리" 인데, Center Eye의 정직한 정면 방향이 아니라 -XZ평면에 투영시킨- 고정된 Y축 값의 정면방향인 것입니다.

추가로, 이렇게 하면 UI의 Y축 값은 현재 사용자의 Y 값과 일치하게 됩니다. Y값을 임의로 조금 낮게 또는 높게 변경하고 싶다면, 아래와 같이 코드를 추가해주면 됩니다. 

```c#
canvas.transform.position = camera.position + Vector3.up * height + 
Vector3.ProjectOnPlane( camera.forward , Vector3.up ).normalized * canvasDistance;
```

이렇게 시선 정면에 UI를 띄울 수 있습니다.

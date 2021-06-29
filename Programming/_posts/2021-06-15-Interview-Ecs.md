---
layout: post
title: '면접 질문 정리 4 - ECS와 Job System'
tags: [Unity]
image : /assets/img/post/2021-06-15-Interview-Ecs/main.jpg
description: >
  유니티의 새로운 기술 스택인 DOTS와 그 안에 ECS와 Job System에 대해 간략히 살펴보겠습니다.
---

* toc
{:toc .large-only}
사실 ECS와 Job System은 유니티에서 다음 단계로 제시한 개발 패터다임이기 때문에 유니티를 이용한 개발에 관심이 있는 사람이라면 한 번쯤은 들어봤을 것입니다. 하지만 첫 글에서도 말했듯이 대충 아는 것으로는 아무것도 안다고 말할 수 없기에 ECS와 Job System에 대한 정확한 정의와 설명을 정리해 보려고 합니다. 기회가 된다면 샘플 코드도 볼 수 있으면 좋을 것 같습니다.

## DOTS

ECS와 Job System을 말하려면 먼저 DOTS에 대해서 간략하게 이야기해야 합니다. DOTS는 **D**ata-**O**riented **T**echnology **S**tack의 약자로 데이터 지향 기술 스택으로 번역됩니다. 기존의 프로그램은 객체 지향(OOP, **O**bject **O**riented **P**rogramming)입니다. 객체 지향은 객체에 중점을 두기 때문에, 객체에 어떤 데이터가 있는지, 메모리 어디에 위치해있는지 등은 관심이 없습니다. 이런 특성들이 캡슐화를 통해서 반영이 됩니다. 반면, 데이터 지향의 경우 객체를 데이터로 변환해서, 데이터의 타입이 어떤지, 메모리에 어떻게 배치할 것인지 등 데이터 자체를 중점으로 살피는 방식입니다. 따라서 기존의 객체 지향적 사고방식을 바꿔야 하는 어려움이 있지만, 성능 면에서 뛰어난 이점을 가질 수 있는 방식입니다. 

DOTS를 가장 빠르게 이해할 수 있는 방법은 1억 개의 물체를 회전시키는 예제를 통해 알 수 있습니다. 기존의 방식은 모든 GameObject를 돌며, 해당 GameObject의 Component에서 Rotate() 라는 함수를 실행시켜주어야만 했습니다. 객체가 중점이기 때문에, 각 객체가 가지는 함수를 실행하는 방식이었습니다. 이러한 방식은 1억 개의 객체가 힙 메모리 속에 혼재되어 있고, 각 객체의 메서드 또한 중간중간 끼어있기 때문에, 굉장히 느리게 작동했습니다. 하지만 DOTS에서는 Rotate()라는 하나의 함수에 1억 개의 물체의 데이터만을 동일한 함수에 계속해서 집어넣는 형식입니다. 이렇게 한다면 1억 개의 물체는 객체가 아니라 데이터만을 가지고 있는 구조체가 되고, 구조체의 배열이기 때문에 순차적으로 스택 메모리에 잘 정리됩니다. 당연히 성능에서도 효과를 볼 수 있을 것입니다. 이렇듯 DOTS를 활용하면 대량의 물체를 관리할 때, 기존의 시스템에 비해서 고성능을 낼 수 있습니다. 유니티에서 DOTS 샘플로 발표한 [Mega City](https://unity.com/kr/megacity)는 이러한 특성을 잘 살린 샘플입니다.

DOTS에는 ECS와 Job System뿐만 아니라, Burst Compiler, 새로운 Network, Physics, Audio 등이 포함되어 있습니다. <sub>[유니티 DOTS 패키지](https://unity.com/kr/dots/packages#dots-runtime-preview){:target="_blank"}</sub>

## ECS

ECS는 앞서 말씀드린 DOTS에서 가장 중요한 요소 중의 하나입니다. **E**ntity **C**omponent **S**ystem의 약자로 Entity, Component, System이라는 요소를 가진 새로운 컴포넌트 시스템입니다. 유니티는 언제나 컴포넌트 중심 개발 구조를 가지고 있었습니다. Component가 부착된 GameObject의 집합으로 모든 유니티 프로그램을 설명할 수 있죠. 

ECS는 이러한 컴포넌트 구조에서 데이터 레이아웃을 최적화합니다. 기존의 객체 중심으로 관리되던 데이터를 따로 분리해서, 데이터만을 관리하는 시스템을 만든 것입니다. 위에서 얘기한 예시를 코드로 보자면 

```csharp
class Rotator : MonoBehaviour
{
	public Quaternion rotateVelocity;

	void Update()
	{
		//please ignore this math is all broken, that's not the point here :)
		Quaternion currentRotation = GetComponent<Transform>().rotation;
		GetComponent<Rigidbody>().MoveRotation(Quaternion.Normalize(currentRotation * rotateVelocity));
	}
}
```

이렇게 했을 때의 문제점은 사용하는 `Transform`, `Rigidbody`와 같은 Component들이 메모리 내에 완전히 다른 영역에 위치하고 있기 때문에, 참조하는 데 시간이 오래 걸리고, 캐싱을 한다 해도 컴포넌트가 제거되지 않았는지 확인해야 합니다. 

ECS에서는 조금 다른 방식을 사용합니다.

필요한 Component들을 하나의 세트로 묶습니다. 위에서는 `Transform`, `Rigidbody`가 되겠죠. 이 세트를 Entity라고 부르고, 월드의 모든 이러한 세트를 모아서 만든 배열을 Archetype이라고 부릅니다. 그리고 System을 만드는데, System은 원하는 Component를 가진 Archetype안에 포함된 Entity들로부터 데이터를 받아와서 해당 함수를 실행합니다.

## Job System

위에서 말한 일련의 프로세스들은 Job이라는 작은 단위로 관리되고 실행됩니다. Job은 기존의 Thread를 대체합니다. 

Job System은 여러 코어에 워커 스레드 그룹을 만들어서 관리합니다. 모든 Job들은 Job Queue에 배치되어 실행되게 되는데, 각 워커 스레드는 Job Queue에서 Job을 가져와서 실행합니다. 당연히 여러 코어에 걸쳐서 워커 스레드가 실행되므로, 자연스럽게 멀티 코어 프로그래밍이 가능해집니다.

## 한마디 정리

- ECS와 Job System은 기존 객체 중심적 개발이 아닌, 데이터 중심의 개발 방식은 DOTS의 일부입니다.
- ECS는 Entity Component System의 약자로 객체인 오브젝트가 가진 컴포넌트를 조회하며 동작을 실행하는 것이 아니라, 오브젝트의 데이터만을 수집하고, 데이터를 적절한 함수 파이프라인에 순차적으로 동작시키는 새로운 컴포넌트 시스템입니다.
- Job System은 기존의 Thread를 대체하는 Job을 Job Queue에 넣은 후, 각 코어에 생성된 워커 스레드가 Job Queue로부터 Job을 받아와서 수행하는 멀티 코어 수행 방식입니다.

---

#### 참고자료

[유니티 블로그 : **DOTS 기술 소개: 엔티티 컴포넌트 시스템**](https://blog.unity.com/kr/technology/on-dots-entity-component-system){:target="_blank"}

[EveryDay.DevUp님의 블로그 시리즈](https://everyday-devup.tistory.com/67){:target="_blank"}

[유니티 DOTS](https://unity.com/kr/dots){:target="_blank"}

[유니티 매뉴얼 : 잡 시스템이란?](https://docs.unity3d.com/kr/current/Manual/JobSystemJobSystems.html){:target="_blank"}

---
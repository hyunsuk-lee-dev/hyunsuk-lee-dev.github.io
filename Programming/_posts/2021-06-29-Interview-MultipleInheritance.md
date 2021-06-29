---
layout: post
title: '면접 질문 정리 6 - C# 다중 상속을 지원하지 않는 이유'
tags: [태그]
image : /assets/img/post/2021-06-29-Interview-MultipleInheritance/main.jpg
description: >
  C#에서는 다중상속이 불가능합니다. 왜 C#은 다중상속을 불가능하도록 만들었는지 알아봅시다.
---

* toc
{:toc .large-only}
개발을 하다가 보면 다중상속이 있으면 좋겠다고 생각할 때가 많이 있습니다. A에 구현해 놓은 기능을 쓰고 싶은데, B에 구현해놓은 기능도 함께 쓰고 싶을 때 말입니다. 하지만 안타깝게도, C# 은 다중상속을 지원하지 않습니다. 

왜 다중상속에 어떤 문제가 있길래 지원하지 않는 것일까요.

## 이름의 중복

가장 1차원적인 문제는 변수 이름이 중복된다는 것입니다.

```csharp
public class Camera
{
    public int price = 1000;
}

public class Calculator
{
    public int price = 500;
}

public class SmartPhone : Camera, Calculator
{
	public void ShowPrice()
    {
        Console.WriteLine(price);
    }
}
```

과연 스마트폰의 가격은 1000원일까요, 500원일까요. 

이처럼 다중상속을 사용하게 되면, 둘 이상의 부모클래스가 생기게 되는데, 부모클래스의 변수나, 함수 등을 사용할 때 어떤 부모의 것을 취할 것인지 결정할 수 없는 문제가 생기게 됩니다.

## 다이아몬드 문제

이와 비슷한 또 다른 이유는 다이아몬드 문제 때문입니다. 영어로는 **Diamond of Death**. 즉, **죽음의 다이아몬드**라고 불리는 이 문제는 상속 계층도가 다이아몬드처럼 생겨서 붙여진 이름입니다. 

![다이아몬드 모양](/assets/img/post/2021-06-29-Interview-MultipleInheritance/hierarchy.png){:.center width="400px"}
상속 계층이 다이아몬드 모양을 하고 있습니다.
{:.figcaption}

```csharp
public abstract class Liquid
{
    public abstract string GetColor();
}

public class Coffee : Liquid
{
    public override string GetColor()
    {
        return "Brown";
    }
}

public class Milk : Liquid
{
    public override string GetColor()
    {
        return "White";
    }
}

public class Latte : Coffee, Milk 
{
    public override string GetColor()
    {
        // White or Brown or LightBrown ?? 
        return base.GetColor();
    }
}
```

사실 이름은 거창하지만, 문제는 아주 단순합니다. 과연 라떼는 어떤 색깔일까요? 이러한 문제 때문에, 최근의 언어들에서는 다중상속을 불가능하게 막아놓았습니다.

## 인터페이스는 가능하다.

추상클래스와 다르게 인터페이스는 다중상속이 가능합니다. 당연한 말이겠지만, 인터페이스는 함수의 구현이 없기 때문입니다. 그러니 둘 이상의 인터페이스를 상속받더라도 둘 중에서 어떤 인터페이스의 함수를 사용할 지 고민하지 않아도 됩니다. 인터페이스에는 구현된 함수가 없기 때문에, 자식 클래스에서 구현한 함수를 사용할 수 밖에 없으니 말입니다.

## C++도 가능하다.

C#과 마찬가지로 JAVA도 다중상속을 지원하지 않습니다. 그런데 C++은 다중상속을 지원합니다. 물론 다중상속을 사용할 때, 위와 같은 이슈들을 개발자가 직접 처리해주어야 합니다. 아마도 개발자가 더 잘 코딩을 할 수 있을 것이라고 믿기 때문일 것입니다. 

---
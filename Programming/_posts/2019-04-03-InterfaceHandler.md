---
layout: post
title: Interface Handler 활용하기
tags: [Unity, UI]
---

* toc
{:toc .large-only}

## Unity UI Button

Unity에서는 이전에 비해 UI 기능이 많이 발전해왔습니다. 여전히 발전중이고요. Unity로 컨텐츠를 만들 때 가장 빈번하게 쓰이는 UI는 아마 버튼일 것 입니다. 

![Button-Inspector](/assets/img/post/2019-04-03-InterfaceHandler/Button.png "Button"){:.center}

Unity는 자체적으로 Button 이라는 Component를 제공하고 있으며, 이를 활용하여 다양한 버튼 기능을 구현할 수 있습니다. 포인터가 올라갔을 때, 또는 버튼이 선택되었을 때 등의 상황에서 Color는 물론, Sprite를 바꾸기도 하며, Animation Clip을 실행해주기도 합니다. 

## Custom Button 

하지만 이런 Button Component로도 구현할 수 없는 것들이 있습니다. 크기나 방향등이 바뀐다거나, 내용이 바뀐다거나, 새로운 물체가 생기고 없어지는 등의 자유로운 기능을 추가하지는 못합니다. 

그래서 직접 Button의 기능들을 구현해야 할 때 필요한 것이 Interface Handler 입니다. 

~~~c#
using UnityEngine;
using UnityEngine.EventSystems;

public class InterfaceHandlerSample : MonoBehaviour, IPointerEnterHandler,
IPointerExitHandler, IPointerDownHandler, IPointerUpHandler, IPointerClickHandler
{  
    public void OnPointerEnter(PointerEventData eventData)
    {
        //state = State.Entered;
    }
    
    public void OnPointerExit(PointerEventData eventData)
    {
        //state = State.Exited;
    }

    public void OnPointerDown(PointerEventData eventData)
    {
        //state = State.Down;
    }

    public void OnPointerUp(PointerEventData eventData)
    {
        //state = State.Up;
    }

    public void OnPointerClick(PointerEventData eventData)
    {
        //state = State.Click;
    }
}
~~~

Pointer가 UI에 들어왔을 때(Hover), 특정 기능이 동작하도록 구현하고 싶다면, `IPointerEnterHandler` 라는 인터페이스를 상속하고, `OnPointerEnter(PointerEventData eventData)` 라는 함수를 구현하면 됩니다. 

Exit과 Down, Up, Click 역시 마찬가지입니다. 

실제로 Unity에서 제공하는 Button이라는 Component도 이러한 Interface들을 이용해서 만들어졌습니다. 

~~~c#
using UnityEngine.Events;
using UnityEngine.EventSystems;

namespace UnityEngine.UI
{
    [AddComponentMenu("UI/Button", 30)]
    public class Button : Selectable, IPointerClickHandler, ISubmitHandler, IEventSystemHandler
    {
        protected Button();

        public ButtonClickedEvent onClick { get; set; }

        public virtual void OnPointerClick(PointerEventData eventData);
        public virtual void OnSubmit(BaseEventData eventData);

        public class ButtonClickedEvent : UnityEvent
        {
            public ButtonClickedEvent();
        }
    }
}
~~~

예시 코드로 나온 기본적인 Button을 구성하는데 필요한 Interface들외에도 UnityEngine의 EventSystems 네임스페이스에는 [여러 다양한 Interface들](https://docs.unity3d.com/2019.1/Documentation/Manual/SupportedEvents.html)이 구현되어 있고, 이를 상속함으로써 다양한 기능들을 구현할 수 있습니다. 

Drag interface로 윈도우 아이콘 같이 원하는 위치에 놓을 수 있는 아이콘 UI를, Scroll interface를 이용해서 Scroll Window를 직접 만들어볼 수도 있습니다.
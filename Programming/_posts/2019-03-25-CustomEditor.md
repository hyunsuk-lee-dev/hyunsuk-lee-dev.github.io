---
layout: post
title: CustomEditor 활용하기
tags: [Unity, Editor]
---

* toc
{:toc .large-only}

## CustomEditor 기능은 언제 필요할까요.  

Unity에서 테스트를 할 때, 유용하게 사용할 수 있는 것이 바로 UnityEditor 안에 있는 CustomEditor 기능입니다.   
특별히 VR에서의 프로그램을 개발할 때는 더욱이 필수적인 요소라고 생각합니다. 왜냐하면, VR 프로그램에서 테스트를 하려면, 직접 HMD를 착용하고 컨트롤러를 들어서 UI를 누르거나 물체를 옮기거나 하는 등의 행동을 해야하는데, CustomEditor의 Button 기능을 구현한다면, 테스트를 위해서 VR 기기를 착용하고 직접 컨트롤러를 조작하지 않고도, 마우스로 버튼을 클릭하여서 구현한 기능이 제대로 동작하는지 확인할 수 있기 때문입니다.  

구현 코드는 매우 간단합니다.  

~~~c#
//TestScript.cs

using UnityEngine;  
using UnityEditor;

public class TestScript : MonoBehaviour  
{
    public Transform sample;
    
    public void MoveForwardSample()
    {
        sample.Translate(transform.forward * 3f);
    }

    public void MoveRightSample()
    {
        sample.Translate(transform.right * 3f);
    }
}

[CustomEditor(typeof(TestScript))]
public class TestScriptCustomEditor : Editor
{
    TestScript script;

    private void OnEnable()
    {
        script = (TestScript)target;
    }

    public override void OnInspectorGUI()
    {
        base.OnInspectorGUI();

        if(GUILayout.Button("Forward"))
        {
            script.MoveForwardSample();
        }
        else if(GUILayout.Button("Right"))
        {
            script.MoveRightSample();
        }
    }
}
~~~ 

위와 같이 만든 스크립트를 GameObject에 붙이면  

![Inspector](/assets/img/post/2019-03-25-CustomEditor/Inspector.png "Custom Inspector")

위와 같은 Inspector를 볼 수 있습니다. 물론 Inspector에서 버튼을 누르면 Object가 동작하게 됩니다.  

## 주의해야 합니다.  

CustomEditor를 사용할 때 주의해야 할 점은, 두번째 줄에 보이는 것 처럼 UnityEditor 네임스페이스를 사용했는데, 이는 Editor에서만 존재하는 네임스페이스입니다. 즉 이는 빌드를 할 시에 오류를 유발합니다.  

![Error](/assets/img/post/2019-03-25-CustomEditor/EditorError.png "Editor Error"){:.center}

이것을 해결하기 위해서는 2가지 방법이 있습니다.  
- 첫번째로는 [프리컴파일 코드](https://docs.unity3d.com/kr/2019.1/Manual/PlatformDependentCompilation.html){:target="_blank"}를 이용해서 해당 코드블럭을 Editor로 한정시키는 것입니다.  

~~~c#
#if UNITY_EDITOR
//Editor Code
#endif
~~~

이렇게하면 #if 문 안에 있는 코드들은 Editor에서는 실행이 되나, 빌드시에는 제외를 시킬 수 있으므로 오류를 해결할 수 있습니다. CustomEditor가 시작되는 21번째 줄 부터 마지막인 44번째줄까지 묶어주고, 또한 그와 같이 네임스페이스 부분인 4번째 줄도 동일하게 해주시면 됩니다.  

- 두번째 방법은 Editor 폴더를 사용하는 방법입니다.  

Unity는 Assets 안에 있는 [폴더들의 이름을 사용하여 특별하게 취급하는 기능](https://docs.unity3d.com/kr/2019.1/Manual/SpecialFolders.html){:target="_blank"}이 있습니다. 그 중의 하나가 Editor 입니다. Unity는 Editor라는 이름을 가진 폴더 아래에 있는 에셋들은 오직 Editor에서만 사용한다고 취급하여 빌드시에는 제외합니다. 이를 이용하여, 위의 코드중 CustomEditor 부분만을 빼내어서 `TestScriptCustomEditor.cs` 의 이름의 C# 스크립트를 새로 만들어 Editor 폴더 안에 넣어주면 에러를 해결할 수 있습니다.  

---
layout: post
title: SteamVR HMD에서 Proximity 센서 읽기
tags: [Unity]
image: /assets/img/post/New Unity.jpg
description: >
  Vive를 비롯한 기타 SteamVR HMD를 썼는지 안썼는지 판별해봅시다.
---

* toc
{:toc .large-only}

## Proximity Sensor가 필요한 이유

VR에서 자주 쓰이는 기능 중 하나가 Proximity Sensor의 기능입니다. 

HMD 안쪽, 이마 부분을 보면 센서가 하나 위치하고 있습니다. 이것이 Proximity Sensor 즉, 근접 센서입니다. 이것을 통해서 사용자가 HMD를 착용했는지, 벗었는지 알 수 있습니다.
VR은 일반적으로 프로그램이 시작되었을 때, 컨텐츠가 시작되는 것이 아니라, 사용자가 HMD를 착용했을 때 컨텐츠가 시작하는 것이 알맞은 사용자 경험입니다. 또한 반대로, 사용자가 HMD를 벗었을 때는 컨텐츠가 종료되거나, 일시정지 되어야 합니다.

이러한 이유로, Proximity Sensor의 값을 아는 기능은 언제나 필요합니다.

## 유니티에서 SteamVR Plugin Import 하기

처음 유니티를 켜고, 새로운 프로젝트를 만들어줍니다. 저는 2019.4.13 버전에, 이름은 Proximity Test라고 짓겠습니다.

![Unity Project](/assets/img/post/2021-01-22-ProximitySensor/Unity Project.png "Unity Project"){:.center}

프로젝트 생성하기
{:.figcaption}

프로젝트가 다 만들어지면 에셋스토어에서 SteamVR Plugin을 Import 해줍니다. 저는 글을 작성하는 1월 22일 기준, 가장 최신 버전인 2.7.2 버전을 사용했습니다. 

![SteamVR Import](/assets/img/post/2021-01-22-ProximitySensor/SteamVR Import.png "SteamVR Import"){:.center}

SteamVR Import
{:.figcaption}

![SteamVR Import](/assets/img/post/2021-01-22-ProximitySensor/SteamVR Import2.png "SteamVR Import"){:.center}

SteamVR Import
{:.figcaption}

임포트하는 중간에 어떤 XR을 사용할 것인지 묻는 창이 나오는데, Unity XR을 선택해줍니다. 

![UnityXR](/assets/img/post/2021-01-22-ProximitySensor/UnityXR.png "UnityXR"){:.center}

UnityXR 선택
{:.figcaption}

그러면 설치가 완료되면서 재시작해달라는 창이 나옵니다. 재시작을 해줍시다. 

![UnityXR Restart](/assets/img/post/2021-01-22-ProximitySensor/UnityXR Restart.png "UnityXR Restart"){:.center}

재시작하기.
{:.figcaption}

프로젝트를 재시작하면 Project 창에 `SteamVR`, `SteamVR_Resources`, `StreamingAssets`, `XR` 폴더가 생긴것을 볼 수 있습니다. 

![ProjectWindow](/assets/img/post/2021-01-22-ProximitySensor/ProjectWindow.png "ProjectWindow"){:.center}

이제 마지막으로 Input 설정을 하겠습니다. 상단 메뉴바에서 `Windows -> SteamVR Input`을 눌러줍니다.

![SteamVR Input Menu](/assets/img/post/2021-01-22-ProximitySensor/SteamVR Input Menu.png "SteamVR Input Menu"){:.center}

그러면 이렇게 Example 파일을 만들겠냐는 문구가 나옵니다. Yes를 눌러 만들어줍니다.

![Example Input Files](/assets/img/post/2021-01-22-ProximitySensor/Example Input Files.png "Example Input Files"){:.center}

Yes 선택
{:.figcaption}

자동으로 생성된 Example Input에서 HeadsetOnHead가 있는지 확인한 후, 꺼줍니다.

![SteamVR Default Input](/assets/img/post/2021-01-22-ProximitySensor/SteamVR Default Input.png "SteamVR Default Input"){:.center}

HeadsetOnHead
{:.figcaption}


## SteamVR Input 사용하기

이제  SteamVR Input을 사용하기 위한 세팅이 모두 끝났습니다. 

이제 생성된 SteamVR Input 값을 받아오는 스크립트를 작성해봅시다.

코드는 간단합니다.

```csharp
public class HeadsetOnHead : MonoBehaviour
{
    private SteamVR_Action_Boolean headsetOnHead;

    private void Start()
    {
        headsetOnHead = SteamVR_Actions.default_HeadsetOnHead;
    }

    private void Update()
    {
        Debug.Log(headsetOnHead.GetState(SteamVR_Input_Sources.Any) ? "On" : "Off");
    }
}
```

기본 씬에, MainCamera 대신 Camera Rig를 넣어주고, 새로 만든 스크립트를 붙여주면 됩니다.

플레이하고 HMD의 센서를 손으로 가려보면서, Console 창에 적합한 결과가 나오는지 확인해보세요.
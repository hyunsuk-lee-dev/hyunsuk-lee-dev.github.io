---
ilayout: post
title: '클립보드에 이미지 설정하기'
tags: [개발]
image : /assets/img/post/2021-03-29-ClipboardImageSaver/Main.jpg
description: >
  윈도우 클립보드에 원하는 이미지를 설정해서 빠르게 복사 및 붙여넣기를 실행해보자.
---

* toc
{:toc .large-only}

## 윈도우 캡쳐기능에 대해서

윈도우 10 부터 윈도우에는 **캡처 및 스케치<sub>(Snip&Sketch)</sub>** 라는 좋은 스크린샷 프로그램이 기본적으로 내장되어있습니다. 기존에 사용하던 캡처도구는 직접 프로그램을 실행시켜야했다면 이 새로운 **캡처 및 스케치**는 단축키로 간단하게 불러올 수 있습니다. 기본 설정된 단축키는 <span class="icon-windows8"></span> + Shift + S 입니다. 

![이미지](/assets/img/post/2021-03-29-ClipboardImageSaver/SnipAndSketch.png){:.center width="700px"}

캡처 및 스케치
{:.figcaption}

단축키를 눌러보면 이처럼 나와서 전체화면이나, 특정창  또는 특정 영역을 캡처합니다. 캡처한 이미지는 파일로 저장되지 않고, 클립보드에 복사가 됩니다.

문제는 여기서 발생합니다. 파일로 저장되지 않는 점이 어떨 때는 장점이 되기도 하지만<sub>-특정화면을 카톡이나 슬랙 같은 메신저로 보여주기 위해서 캡처할 때-</sub>, 또 어떨 때는 단점이 되기도 합니다. 1-2장 캡처할 때야, 그림판에 붙여넣기 해서 저장하면 되지만, 여러화면을 계속해서 캡처하고 저장하려 할 때는 이 과정이 매우 번거롭게만 느껴집니다. 

그래서 **캡처 및 스케치**가 자동으로 클립보드에 복사한 이미지를 저장하는 프로그램을 만들어 보기로 합니다. 

---

## Clipboard 클래스

관련 기능들을 찾아보니 이미 .NET에는 [Clipboard](https://docs.microsoft.com/ko-kr/dotnet/api/system.windows.clipboard?view=net-5.0){:target="_blank"}라는 클래스를 제공하고 있어서 손쉽게 구현이 가능했습니다.

기본적으로 현재 클립보드에 저장된 데이터가 이미지인지를 판별하기 위한

 ```c#
 public static bool ContainsImage ();
 ```

함수와 현재 클립보드에 저장된 이미지 데이터를 얻기 위한

 ```C#
 public static System.Windows.Media.Imaging.BitmapSource GetImage (); 
 ```

함수를 사용합니다.  

추가로 이미지를 저장한 경로를 클립보드에 복사해 추후 유용하게 사용할 수 있도록 

```c#
public static void SetText (string text);
```

함수까지 사용했습니다.



최종적으로 사용된 코드는 아래와 같습니다.

```c#
//Save Clipboard Image
if(Clipboard.ContainsImage())
{
	Clipboard.GetImage().Save(FileName, imageFormat);
	Process.Start("explorer.exe", path);
	Clipboard.SetText(path);
}
```



## 활용까지

물론 이후에 이 프로그램이 백그라운드에서 단축키 입력을 받아 동작 할 수 있도록 HotKey를 등록해주고, 후킹하는 기능도 추가해야합니다. 또한, 불필요한 윈도우를 숨기고 상태표시줄에 NotifyIcon을 만들어주는 기능도 필요합니다. 그리고 단축키를 변경하거나, 저장할 경로 및 파일이름을 변경하는 UI도 있으면 더할 나위 없겠죠. 

하지만 그러한 기능들은 차치하고 Clipboard라는 클래스만으로도 충분히 유용하고 편리한 프로그램들을 만드실 수 있을 것입니다. 예를들면, 자주 사용하는 비밀번호를 원클릭으로 클립보드에 복사해서 Ctrl+V만으로 빠르게 로그인을 한다던지, 콘텐츠의 특정 장면의 이미지를 클립보드에 자동으로 넣어 공유하기 편하게 해주는 기능들을 만들 수 있습니다.

제가 사용한 코드는 [제 리포지토리](https://github.com/leehs27/ClipboardImageSaver){:target="_blank"}에서 확인하실 수 있습니다. 물론 아직 미완이지만, 저는 잘 사용하고 있습니다. 참고하셔서 삶을 조금 더 편리하게 해주는 프로그램들을 한번 만들어보시면 좋을 것 같습니다.


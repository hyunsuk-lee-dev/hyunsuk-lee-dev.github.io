---
layout: post
title: SublimeText3에서 Markdown을 pdf로 변환하기
tags: [기타]
image: /assets/img/post/2021-01-21-MarkdownToPdf/MarkdownLogo.png
---

* toc
{:toc .large-only}
## Markdown을 왜 Pdf로 변환하죠?

개발자에게 Markdown 은 굉장히 편한 문서 작성 언어가 될 수 있습니다. 일반 워드프로세서들에서는 일일이 마우스로 클릭해줘야하는 제목 및 소제목 구분, 볼드 및 이탤릭, 구분선, 목록과 같은 것들을 키보드 입력으로만 손쉽게 처리할 수 있기 때문입니다. 다만 한 가지 단점은, 해당 문서를 회사의 다른 사람들과 공유할 때(특히 개발자가 아닌 분들), 그들에게는 Markdown 파일이 익숙치 않고, 열기도 힘들다는 점이 있습니다. 이를 위해서 가지고 있는 Markdown 파일을 Pdf 파일로 변환해서 공유하는 것이 바람직합니다.

## 일단 설치하자.

저는 텍스트 편집기로 Sublime Text 3를 즐겨 사용하는 편입니다. 왜냐하면 Sublime Text 3 는 서드파티 기능에 대한 지원이 뛰어나기 때문이죠. 물론 그 이외에도 여러가지 장점이 있습니다. 그래서 Markdown 파일 역시 Sublime Text 3를 이용해서 작성을 합니다.

오늘은 Sublime Text 3에서 Markdown 파일을 Pdf 파일로 내보낼 수 있는 기능을 추가해보도록 하겠습니다.

먼저 Pandoc을 설치해주도록 합니다. [Pandoc 설치사이트](https://pandoc.org/installing.html){:target="_blank"}로 이동해서 최신 버전의 인스톨러를 다운받고 실행시켜 Pandoc을 설치합니다.
또 한가지 추가로 필요한 것이 *xelatex* 인데 이는 MiKTex를 설치해서 받을 수 있습니다. 마찬가지로 [MiKTex 설치사이트](https://miktex.org/download){:target="_blank"}에서 윈도우용 MiKTex 인스톨러를 다운받은 후 실행시켜 설치합니다.

> Tex Live도 동일한 기능을 하지만, *LaTeX*을 사용하기 위함이 아니라 단순 Pdf 변환 용도로 사용할 예정이라면 개인적으로는 MiKTex가 설치가 조금 더 빠르고 가볍게 진행할 수 있습니다.

모든 설치가 완료되면, 명령 프롬프트창에서 테스트를 해봅니다.

```bash
> pandoc test.md --pdf-engine xelatex -o test.pdf
```

![Package Install](/assets/img/post/2021-01-21-MarkdownToPdf/PackageInstall.png "Package Install"){:.center}

실행하면 패키지를 추가로 설치합니다. 몇가지가 나오는데 계속 설치해줍니다. 모든 설치가 완료되면 변환이 완료가 됩니다.

한글을 포함한 Markdown 문서를 변환할 때, 에러가 나는 경우가 있습니다. 그럴 때는 명령어에 메인폰트를 지정해주어야합니다.

```bash
> pandoc test.md --pdf-engine xelatex -o test.pdf -V mainfont:notosanscjkkr-medium.otf
```

> 이외의 폰트 관련 옵션은 [Pandoc 사이트](https://pandoc.org/MANUAL.html#fonts){:target="_blank"}에서 확인 하실 수 있습니다.

## Sublime Text 3에서 사용해보자.

테스트를 통해 기능 확인이 완료되었다면 매번 명령 프롬프트에서 실행하는 것이 아니라, 편집기인 Sublime Text 3에서 자동으로 실행할 수 있도록 설정을 합니다.

상단의 `Tools > Build System > New Build System...` 을 클릭해 새로운 빌드 시스템을 만듭니다.

```json
{
	"cmd": "pandoc \"$file_name\" --pdf-engine xelatex -o \"$file_base_name.pdf\" -V mainfont:notosanscjkkr-medium.otf",
	"selector": "text.html.markdown",
	"shell": true
}
```

적절한 이름으로 저장합니다.

다시 원래의 Markdown 파일로 돌아와서, `Tools > Build System > 새로 만든 빌드 시스템` 을 선택하신 후 `Ctrl + B `를 눌러 빌드를 실행해주면, 동일한 경로에 Pdf 파일이 생성됩니다. 10초 정도 소요되는 것 같습니다.

## 마무리

이렇게 간단하게 SublimeText3를 이용해서 Markdown 파일을 Pdf 파일로 변환하는 방법에 대해서 알아봤습니다.

사실 결과물이 조금은 마음에 들지 않는 모양이기도 합니다. 좌우 및 상하 여백이 너무 넓고, 폰트도 수정하고 싶고.. 해당 옵션들은 Pandoc 공식 문서를 참고하셔서 세팅을 마치실 수 있습니다.

> 참고로, Markdown을 Pdf 로 변환하는 가장 간단한 방법은 [변환 기능을 제공하는 웹사이트](https://www.markdowntopdf.com/){:target="_blank"}를 이용하거나  Markdown 전용 편집기인 Typora 에서 내보내기 기능을 사용하는 것입니다. 다만 위의 방법들은 조금 번거롭거나, 원하는 스타일이나 기능이 되지 않을 수 있습니다. 저도 처음에는 Typora의 내보내기 기능을 이용했었는데, 주석처리가 적용이 안되는 문제가 있어서 Pandoc을 사용하게 되었습니다.
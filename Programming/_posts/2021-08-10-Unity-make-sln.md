---
layout: post
title: '(짧은팁)유니티가 비쥬얼스튜디오 솔루션을 안만들 때'
tags: [Unity]
image: /assets/img/post/Unity2021Wide.png
description: >
  유니티가 비쥬얼 스튜디오 솔루션을 만들어주지 않아요!
---

* toc
{:toc .large-only}

### 유니티가 비쥬얼 스튜디오 솔루션을 만들어주지 않아요ㅠㅜ

회사에서 새로운 PC로 바꾸느라, 새로운 PC에 유니티를 새로 설치하고, 비쥬얼 스튜디오도 새로 설치하게 되었습니다.

그렇게 새로운 유니티를 설치하고, 새로운 프로젝트를 만들어서 진행하려고 했습니다....

그런데, 이게 무슨 일인지, 새로운 프로젝트에 비쥬얼스튜디오 솔루션 파일이 생기지 않았습니다. 스크립트를 실행해도, 스크립트 하나만 열리고 솔루션이 열리지 않는 현상이 생겼습니다.



### 해결법은 간단했습니다.

유니티의 ***Preferences***에서 ***External Tools***설정을 바꿔줘야했습니다.

원래는 'Open by file extension'으로 되어있는 것을 'Visual Studio'로 바꿔주시면 솔루션 및 프로젝트 파일 생성옵션이 활성화되면서 해결됩니다.

![Preference]({{ page.asset_path }}/Preference.png){:.center}

![PrefereceVs]({{ page.asset_path }}/PrefereceVs.png){:.center}

끝!



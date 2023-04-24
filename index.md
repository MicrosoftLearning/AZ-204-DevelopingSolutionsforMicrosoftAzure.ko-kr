---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
ms.openlocfilehash: 8cc220d44fe56f19385b25675c29e391b610db8e
ms.sourcegitcommit: d2d374fffa4fcbf92b9c4bdc9c9ecc470152e033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "138100143"
---
## <a name="content-directory"></a>콘텐츠 디렉터리

아래에는 각 랩의 하이퍼링크 목록이 나와 있습니다.

## <a name="labs"></a>랩

{% assign labs = site.pages | where_exp: "page", "page.url contains '/Instructions/Labs'" %}
| 모듈 | 랩 |
| --- | --- |
{% for activity in labs  %}{% if activity.lab.az204Module %}| {{ activity.lab.az204Module }} | [{{ activity.lab.az204Title }}]({{ site.github.url }}{{ activity.url }}) |
{% endif %}{% endfor %}

## <a name="demos"></a>데모

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| 모듈 | 데모 |
| --- | --- | 
{% for activity in demos  %}| {{ activity.demo.az204Module }} | [{{ activity.demo.az204Title }}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

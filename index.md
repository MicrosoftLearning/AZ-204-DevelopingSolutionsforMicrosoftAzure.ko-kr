---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
---

## 콘텐츠 디렉터리

각 랩에 대한 하이퍼링크는 아래에 나열되어 있습니다.

## 랩

{% assign labs = site.pages | where_exp: "page", "page.url contains '/Instructions/Labs'" %}
| 모듈 | 랩 |
| --- | --- |
랩의 활동에 대한 {% %} {% if activity.lab.az204Module %}| {{ activity.lab.az204Module }} | [{{ activity.lab.az204Title }}]({{ site.github.url }}{{ activity.url }}) |
{% endif %} {% endfor %}

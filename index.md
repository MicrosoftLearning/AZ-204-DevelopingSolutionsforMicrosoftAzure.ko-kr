---
title: 온라인 호스팅 지침
permalink: index.html
layout: home
---

## 콘텐츠 디렉터리

아래에는 각 랩의 하이퍼링크 목록이 나와 있습니다.

## 랩

{% assign labs = site.pages | where_exp: "page", "page.url contains '/Instructions/Labs'" %}
| 모듈 | 랩 |
| --- | --- |
{% for activity in labs  %}{% if activity.lab.az204Module %}| {{ activity.lab.az204Module }} | [{{ activity.lab.az204Title }}]({{ site.github.url }}{{ activity.url }}) |
{% endif %}{% endfor %}

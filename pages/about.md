---
layout: page
title: About
description: 饕尽天下的技术积累
keywords: taojintianxia, Nianjun Sun
comments: true
menu: 关于
permalink: /about/
---

记录下平时的技术积累, 仅此而已

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}

---
title: Azure AI 服務練習
permalink: index.html
layout: home
---

# Azure AI 服務練習

下列練習旨在支援 Microsoft Learn 上的課程模組。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs %} {% unless activity.url contains 'ai-foundry' %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endunless %} {% endfor %}

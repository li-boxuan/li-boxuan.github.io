---
layout: page
permalink: /publications/index.html
title: Publications
pubs:
  - author: "Cheng Wang, Xusheng Chen, Weiwei Jia, Boxuan Li, Haoran Qiu, Shixiong Zhao, and Heming Cui"
    title: "PLOVER: Fast, Multi-core Scalable Virtual Machine Fault-tolerance"
    keywords: "PLOVER"
    month: "April"
    year: "2018"
    address: "Renton, WA"
    booktitle: "15th USENIX Symposium on Networked Systems Design and Implementation (NSDI'18)"
    url: plover.pdf

---

# Publications

{% for pub in page.pubs %}
{% unless pub.hidden %}
  - {% if pub.url %} [{{pub.title}}]({{pub.url}}).
    {% else %} {{pub.title}}.
    {% endif %}{% if pub.type %}({{pub.type}})
    {% endif %}<br>
    {{pub.author}}.<br>
    {% if pub.type == 'Technical Report' %}{{pub.number}}
    {% endif %}{{pub.booktitle}}{{pub.school}}{{pub.journal}}.<br>
    {% if pub.address %}{{pub.address}}.
    {% endif %} {{pub.month}}, {{pub.year}}. {% if pub.slides %}[Slides]({{pub.slides}}).
    {% endif %}{% if pub.bibtex %}[Bibtex]({{pub.bibtex}}).
    {% endif %}
{% endunless %}
{% endfor %}




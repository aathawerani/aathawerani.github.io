---
layout: page
title: Systems
permalink: /blog/categories/systems/
---

{% for post in site.categories.systems %}
### [{{ post.title }}]({{ post.url | prepend: site.baseurl }})
<small>{{ post.date | date: "%b %-d, %Y" }}</small>

{{ post.excerpt }}

---
{% endfor %}

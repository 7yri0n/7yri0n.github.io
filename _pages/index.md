---
layout: defaults/page
permalink: index.html
narrow: true
title: 7yri0n
---

## Sobre este blog

{% include components/intro.md %}


## Sobre mi

Recien titulado en la Escuela de Ingenieria Informática de Valladolid. Actualmente trabajo de analista de ciberseguridad en un CERT. CTF player.

<hr />

### Últimos posts

{% for post in site.posts limit:3 %}
{% include components/post-card.html %}
{% endfor %}



---
title: Documentation
narrow: true
permalink: list/projects.html
show_profile: true
---



<style>
	#menu * { list-style:none;}
	#menu li{ line-height:180%;}
	#menu li a{color:#222; text-decoration:none;}
	#menu li a:before{ content:"\025b8"; color:#ddd; margin-right:4px;}
	#menu input[name="list"] {
	    position: absolute;
	    left: -1000em;
	    }
	#menu label:before{ content:"\025b8"; margin-right:4px;}
	#menu input:checked ~ label:before{ content:"\025be";}
	#menu .interior{display: none;}
	#menu input:checked ~ ul{display:block;}
</style>


<ul id="menu">
   <li><input type="checkbox" name="list" id="nivel1-1"><label for="nivel1-1">Pentesting</label>
   <ul class="interior">

<div markdown="1">

{% assign pentesting = site.pages | where_exp: "item" , "item.path contains 'pentesting'"%}

{% for item in pentesting %}

- [{{item.title }}]({{item.url}})

{% endfor %}

</div>
	</ul>
   <li><input type="checkbox" name="list" id="nivel1-2"><label for="nivel1-2">Análisis de Alertas</label>
   <ul class="interior">

<div markdown="1">

{% assign analisisAlertas = site.pages | where_exp: "item" , "item.path contains 'analisisAlertas/index'"%}

{% for item in analisisAlertas %}

- [{{item.title }}]({{item.url}})

{% endfor %}

</div>
   		
<li><input type="checkbox" name="list" id="nivel2-1" ><label for="nivel2-1">Funcionamiento protocolos</label>

<ul class="interior">

<div markdown="1">

{% assign funcionamientoProtocolo = site.pages | where_exp: "item" , "item.path contains 'analisisAlertas/funcionamiento_protocolo'"%}

{% for item in funcionamientoProtocolo %}

- [{{item.title }}]({{item.url}})

{% endfor %}

</div>

</ul>

</li></ul>
	<li><input type="checkbox" name="list" id="nivel1-3"><label for="nivel1-3">Monitorización (CERT)</label>
	   <ul class="interior">

<div markdown="1">

{% assign monitorizacion = site.pages | where_exp: "item" , "item.path contains 'monitorizacion'"%}

{% for item in monitorizacion %}

- [{{item.title }}]({{item.url}})

{% endfor %}

</div>
	</ul>
	<li><input type="checkbox" name="list" id="nivel1-4"><label for="nivel1-4">Linux</label>
	   <ul class="interior">

<div markdown="1">

{% assign linux = site.pages | where_exp: "item" , "item.path contains 'linux'"%}

{% for item in linux %}

- [{{item.title }}]({{item.url}})

{% endfor %}

</div>
	</ul>
	<li><input type="checkbox" name="list" id="nivel1-5"><label for="nivel1-5">Windows</label>
	   <ul class="interior">

<div markdown="1">

{% assign windows = site.pages | where_exp: "item" , "item.path contains 'windows'"%}

{% for item in windows %}

- [{{item.title }}]({{item.url}})

{% endfor %}

</div>
	</ul>


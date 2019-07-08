---
layout: page
title: SRCC Documentation Portal
permalink: /
---

# Welcome to Stanford Research Computing

These are documentation pages to assist you with using our resources. For
events, staff, and the official Stanford page, please see [srcc.stanford.edu](https://srcc.stanford.edu).

<style>
@import url(https://fonts.googleapis.com/css?family=Roboto:400,300,300italic,100,100italic,400italic,500,500italic,700,700italic&subset=latin,cyrillic);

.wrapper {
	display:flex;
	flex-direction:row;
	flex-wrap:wrap;
	justify-content:center;
}

.box {
	background-color: #fafafa;
	box-shadow: 0px 2px 2px rgba(0,0,0,.2),	0px 0px 2px rgba(0,0,0,.2);
	width: 100%;
	border-radius: 2px;
	margin:20px;
	display:flex;
	flex-direction:column;
	cursor:pointer;
}

.box:hover {
	box-shadow: 0px 15px 20px rgba(0,0,0,.25),0px 0px 30px rgba(0,0,0,.1);
}

.box .text {
	padding: 24px;
}

.box .bb {
	border: 1px solid red;
}

.box .title {
	color:  black;
	font-weight: 500;
	font-size: 20px;
	margin-top: -2px;
	margin-bottom: 16px;
}

.box p {
	color: rgba(0,0,0,.5);
	font-size: 16px;
	line-height: 24px;
	margin: 0px;
}

.box .act {
	padding: 8px 0;
	text-align: right;
}

.card-button {
	text-transform: uppercase;
	display: inline-block;
	font-size: 13px;
	padding: 12px 10px;
	color: #00a5ef;
	font-weight: 500;
	margin-right: 8px;
	cursor: pointer;
}
</style>
<div class="wrapper">
  {% for doc in site.data.metadata %}<div class="box">
  <div class="text">
    <div class="title">{{ doc.name }}</div>
      <p><img src="{{ doc.logo }}" style="height:160px; position:absolute">
       <span style="width:50%; float:right">{{ doc.description }}</span>
      </p>
    </div>
    <div class="act">
	<a href="{{ doc.url }}" target="_blank"><div class="card-button">Documentation</div></a>
    </div>
  </div>{% endfor %}
</div>

For getting started with a general linux cluster, see the {% include doc.html name="Getting Started" path="getting-started" %} page. If you need help or want to improve the site, please [open an issue]({{ site.repo }}/issues) or
contact us at [{{ site.email }}](mailto:{{ site.email }})

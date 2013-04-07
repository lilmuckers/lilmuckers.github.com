---
layout: raw
category : Tech
tagline: "This ain't your grandmas blog engine"
tags : [jekyll, wordpress, thoughts]
summary: "Basic jekyll documentation to get started with the bootstrap setup"
image: http://placekitten.com/940/400
---
{% include JB/setup %}

<div class="page-header">
  <h1>{{ page.title }} {% if page.tagline %}<small>{{page.tagline}}</small>{% endif %}</h1>
</div>

<div class="row-fluid post-full">
  <div class="span12">
    <div class="date">
      <span>{{ page.date | date_to_long_string }}</span>
    </div>
    <div class="content">
      <p>conetent in a span8</p>
    </div>
  </div>
  <div class="span4">
    <p>content in a span4</p>
  </div>
  <div class="span12">
    <div class="content">
      <p>content in a soan12</p>
      <script src="https://gist.github.com/lilmuckers/5328432.js">
        //<!--
        //-->
      </script>
      <script src="https://gist.github.com/lilmuckers/5328532.js">
        //<!--
        //-->
      </script>
    </div>
  </div>
</div>

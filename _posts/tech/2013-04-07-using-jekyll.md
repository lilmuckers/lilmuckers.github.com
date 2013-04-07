---
layout: raw
category : Tech
tagline: "This ain't your grandmas blog engine"
tags : [jekyll, wordpress, thoughts]
summary: "Basic jekyll documentation to get started with the bootstrap setup"
image: /assets/images/posts/using-jekyll/header.png
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
  </div>
  <div class="span12">
    <div class="content">
      <h2>What?</h2>
    </div>
  </div>
  <div class="span8">
    <div class="content">
      <p>So, recently I realised that I wanted to get back into writing my blog in a bit more of a serious manner, so I loaded up my old wordpress blog and I was revolted!. I remembered it being so much shinier than it was, it looked grey and drab, and blegh.</p>
      <p>It was hosted on my own set of servers, and I really didn't want that as a long term solution, because of two things really:</p>
      <ol>
        <li>I want to spend as little as possible on hosting becuase, well, who wants to spend money if they don't have to?</li>
        <li>My personal VPS resources aren't great, and it does impact the performance of wordpress from time to time, even with memcached and apc doing their crazy thing.</li>
      </ol>
    </div>
  </div>
  <div class="span4">
    <img src="/assets/images/posts/using-jekyll/old_site-small.png" title="Yuk! Old Wordpress!" >
  </div>
  <div class="span12">
    <div class="content">
      <hr>
      <h2>Ok, cool, so what's this Jekyll thing?</h2>
      <p><a href="http://jekyllrb.com/" alt="Jekyll Static Site Generator">Jekyll</a> is a blog aware static site generator - which is a complicated way of saying that it takes a bunch of text files and smooges them all together to build a completely static site in flat html files. This means you get the advantage of templating, simple post creation, and so forth, from systems such as WordPress and so forth, with the speed and efficiency of doing everything in flat HTML.</p>

      <p>The handy dandy thing is that <a href="http://pages.github.com/" alt="GitHub Pages" >GitHub Pages</a>, the free hosting that is offered to GitHub members, has Jekyll support, meaning you can submit your site in the form of Jekyll source, and it will be compiled into the static site on the fly. The downside with this is that you are restricted to the Jekyll functionality that GitHub think is appropriate. This is, unfortunately, somewhat limited - as it means you don't get the power of the jekyll plugins, however it's completely understandable, they don't want people executing arbitrary code on their servers.</p>
      
      <hr>

      <h2>Sounds good, I'm interested, tell me more...</h2>
      <p>Well, Jekyll can be pretty intimidating if you're unfamilier with it, so you might want to start with a prebuilt helper framework, such as <a href="http://jekyllbootstrap.com/" alt="Jekyll Bootstrap" >Jekyll Bootstrap</a> or <a href="http://octopress.org/" alt="Octopress">Octopress</a>. However they're really aimed at two different approaches of running a Jekyll website - especially when you're talking about hosting them on GitHub Pages. I'm going to talk about them as if one were thinking of hosting on GitHub Pages.</p>
    </div>
  </div>
  <div class="span6">
    <div class="content">
      <h3>Jekyll Bootstrap</h3>
      <p>This is a simple set of liquid templates and logic set up as a quickstart framework for Jekyll. It has a setup to use templates, and some handy helpers and templates to generate archives, site maps, rss feeds, and similar.</p>
      <p>Because it is fairly basic in its implementation it means that it is completely compatible with the version of Jekyll that is used for the compilation on GitHub pages, this is its biggest advantage for me.</p>
    </div>
  </div>
  
  <div class="span6">
    <div class="content">
      <h3>Octopress</h3>
      <p>This is an entire plugin set, template setup, and suchlike that adds a massive amount of functionality on top of the general default Jekyll functionality set, which is pretty awesome!</p>
      <p>Unfortunately all this extra functionality means that you can't compile them on the GitHub Pages server, and you must do it on your own machine and then commit the compiled files to the github repository. Since I want to be able to create posts through the GitHub web interface by manually creating the post files, this is probably the biggest disadvantage in my eyes to using Octopress.</p>
    </div>
  </div>
  
  <div class="span12">
    <div class="content">
      <hr>
      <h2>So, Jekyll Bootstrap then?</h2>
      <p>Indeed! That's the plan!</p>
      <p>I did a lot of futzing around with the templates in this system so it's pretty unrecognisable as a base Jekyll Bootstrap setup. One of the things i wanted to do was to build a <a href="/#portfolio">portfolio</a> that was kinda content managed, and not just hard coded. For no better reason than "it looked like fun". This is where I ran into my first hurdle with the system.</p>
      
      <h3>Page ordering <small>It's annoyingly inconvenient</small></h3>
      <p>One of the issues with Jekyll (at least the version on GitHub Pages) is that there's very little utility for ordering pages within the system. For example - when running a Jekyll test server on my home computer during the development of this site I was able to order the pages purely by using filenames to achieve the ordering - for example:</p>
      <ul class="unstyled">
        <li><i class="icon-file"> </i> 0-wmg.md</li>
        <li><i class="icon-file"> </i> 1-publiccreative.md</li>
        <li><i class="icon-file"> </i> 2-pod1.md</li>
        <li><i class="icon-file"> </i> 3-senokian.md</li>
        <li><i class="icon-file"> </i> 4-personal.md</li>
      </ul>
      <p>On my home server - they were delivered in the <code>site.pages</code> array in that order, so just putting a number on filename was enough to order the pages to my satisfaction.</p>
      
      <h4>And here's the rub <small>For in that sleep of death what dreams may come?</small></h4>
      <p>Unfortunately, on pushing this to GitHub Pages they ended up being ordered like:</p>
      <ul class="unstyled">
        <li><i class="icon-file"> </i> 3-senokian.md</li>
        <li><i class="icon-file"> </i> 0-wmg.md</li>
        <li><i class="icon-file"> </i> 2-pod1.md</li>
        <li><i class="icon-file"> </i> 4-personal.md</li>
        <li><i class="icon-file"> </i> 1-publiccreative.md</li>
      </ul>
      <p>Which is far from what was desired - infact it makes very little sense as to how they were ordered, they weren't even done by created timestamp, or updated timestamp, there didn't really seem to be a pattern at all! This was most vexing, so I was close to just hardcoding the portfolio section of the homepage.</p>
      <p>However, I was doing some googling and I came across a <a href="https://github.com/plusjade/jekyll-bootstrap/pull/134" alt="Implementation of page/post sorting via optional weight meta-data.">pull request</a> on the Jekyll Bootstrap repo, and it was a very handy little solution to my issue. So the code I implemented was:</p>
      <script src="https://gist.github.com/lilmuckers/5328432.js">
        //<!--
        //-->
      </script>
      <p>My mind rebelled at this solution, somewhat, because as a developer more used to dynamic site building doing iterative for loops like this just scream "inefficiency" to me. However since this is done just once when the page is generated I guess I don't really have to worry about that, it's just extra load on the GitHub servers when I push a page updated, and hey, knowing me that's not going to be all that oftan.</p>
      
      <hr>
      <h2>Dynamic Content <small>Doesn't this mean you'll lose some niceness?</small></h2>
      <p>Well, yes, kinda, it means we don't have the traditional on-demand kind of dynamic things that you get from the server side scripting that I usually embroil myself in. However the nerd in me squeals with barely contained joy that I have to come up with new and interesting ways to solve problems that I typically would have just used some PHP for.</p>
      <p>Whilst I would never be able to implement highly interactive features such as forums or a full CMS, there are certainly <em>some</em> things I can implement. The way I see it there are two possible ways to get some of that niceness back, and which one I use depends on the immediacy of the data being presented:</p>
      
      <h3>Periodic Data Updates</h3>
      <p>On my old site I had a script that used a PHP cURL scraper and a MySQL database to track my Xbox Live achievements on my site in a nice way, this means that the data was only changing whenever I scored a new achievement on Xbox Live. That's very doable, and I have infact <a href="/games/xbox.html" alt="Xbox Live Tracker">implemented</a> it already, and I shall document the processes I used more thoroughly in another post.</p>
      
      <h3>Instantaneous Data Updates</h3>
      <p>The other thing we could require is stuff that does instantaneous data presentation, such as my latest tweet, latest GitHub commit, and so forth. This is the kind of thing that we could use purley client side JavaScript for. This does limit us to using APIs that support either <strong>JSONP</strong> or <strong>Cross Origin Resource Sharing</strong>, but that is definitely doable.</p>
      
      <hr>
      <h2>So... What's your conclusion?</h2>
      <p>Well, Jekyll bills itself as a blogging engine for coders and, well, I'm not entirely sure I agree, to my mind a coder would write their own blog platform, or heavily customise an existing blog platform. That's certainly what I've always done, which is probably why I've never been able to get a blog running for longer than a few weeks at a time, so I have to say this is the next best thing. Plus the fact i'm going to have to get inventive to do some funky functionality on it, I'm kinda happy about it.</p>
      <p>So I'm really looking forward to getting my teeth into this stuff.</p>
    </div>
  </div>
  
  
  {% include custom/post_meta %}
</div>
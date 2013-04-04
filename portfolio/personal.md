---
layout: portfolio
title: "Personal Projects"
tagline: "Nerding is also my hobby :)"
group: portfolio
big_image: /assets/images/portfolio/icons/personal-big.png
small_image: /assets/images/portfolio/icons/personal-small.png
weight: 4
---
{% include JB/setup %}

<div class="row-fluid portfolio-row">
  <div class="span4">
    <img src="/assets/images/portfolio/personal/xbox-tracker-jekyll-small.png" >
  </div>
  <div class="span8">
    <h3>Jekyll Xbox Tracker <small><a href="/games/xbox.html">http://www.patrick-mckinley.com/games/xbox.html</a></small></h3>
    <p>This is a project of mine to allow me to publish my Xbox Live achievements on my website in real-time. Given that Jekyll is a static web system, and the fact I'm hosting it on <a href="http://pages.github.com/">GitHub Pages</a> this means it's not something I can do in the usual way (using a database and some scripting). So instead of that I wrote this tool to publish direct to Jekyll by using the GitHub API.</p>
    <dl>
      <!-- <dt>Blogpost</dt><dd>July 2011</dd> -->
      <dt>Built with</dt><dd>Node.js, GitHub API, YAML</dd>
    </dl>
  </div>
</div>

<hr>

<div class="row-fluid portfolio-row">
  <div class="span8">
    <h3>Githubber <small><a href="https://npmjs.org/package/githubber">https://npmjs.org/package/githubber</a></small></h3>
    <p>A node.js implementation of the GitHub API.</p>
    <dl>
      <dt>GitHub</dt><dd><a href="https://github.com/lilmuckers/github.js">https://github.com/lilmuckers/github.js</a></dd>
      <dt>Built with</dt><dd>Node.js</dd>
    </dl>
  </div>
  <div class="span4">
    <img src="/assets/images/portfolio/personal/octocat-small.png" >
  </div>
</div>

<hr>

<div class="row-fluid portfolio-row">
  <div class="span4">
    <img src="/assets/images/portfolio/personal/issue-converter-small.png" >
  </div>
  <div class="span8">
    <h3>GitHub Issue Converter <small><a href="https://github.com/lilmuckers/issue-pull-request">https://github.com/lilmuckers/issue-pull-request</a></small></h3>
    <p>A web interface for a tool to convert a GitHub issue into a Pull Request. This was required because there is currently no way to do this through the GitHub web interface, despite the fact that, conceptually, they are very similar in the GitHub system. It is possible to use the GitHub API to accomplish this, so I built this tool to do it for my work at Warner Music.</p>
    <dl>
      <!-- <dt>Blogpost</dt><dd>July 2011</dd> -->
      <dt>Built with</dt><dd>Node.js, GitHub API</dd>
    </dl>
  </div>
</div>

<hr>

<div class="row-fluid portfolio-row">
  <div class="span8">
    <h3>Xbox Live Scraper</h3>
    <p>A webservice I built for myself and my friends (and that powers my Jekyll Xbox Tracker) to use <a href="http://phantomjs.org/">PhantomJS</a> to log into Xbox Live and scrape profile, game, and achievement data for the specified user, and then return it as a JSON object.</p>
    <dl>
      <dt>Built with</dt><dd>Node.js, PhantomJS, CasperJS</dd>
    </dl>
  </div>
  <div class="span4">
    <img src="/assets/images/portfolio/personal/xboxlive.jpg" >
  </div>
</div>

<hr>

<div class="row-fluid portfolio-row">
  <div class="span4">
    <img src="/assets/images/portfolio/personal/theporklords-small.png" >
  </div>
  <div class="span8">
    <h3>The Pork Lords <small><a href="http://www.theporklords.com/">http://www.theporklords.com/</a></small></h3>
    <p>I bought the URL after a particularly tangential conversation with a chum of mine at work, and left it at that for a few months. One weekend I decided I wanted to dabble in jQuery and the concept for a floating cloud-pig was born. It doesn’t make a whole deal of sense, but that’s not the point, personally I find it quite soothing to watch, especially since I more recently added some music by the delightful <a href="http://c418.org/">C418</a>.</p>
    <dl>
      <!-- <dt>Blogpost</dt><dd>July 2011</dd> -->
      <dt>Built with</dt><dd>jQuery, HTML</dd>
    </dl>
  </div>
</div>

<hr>

<div class="row-fluid portfolio-row">
  <div class="span8">
    <h3>My Awesome Toolset&trade; <small><a href="https://github.com/lilmuckers/awesome-toolset/">https://github.com/lilmuckers/awesome-toolset/</a></small></h3>
    <p>This started off as an expansion and heavy rewrite of my first xbox-live achievement scraper, but quickly evolved into a framework of its own right. It’s fast, light and flexible, designed mostly for quickly writing command line tools. It currently includes support for Xbox-Live achievement scraping, and Twitter – with the OAuth implementation abstracted out to allow it to be applied in multiple instances. Soon Microsoft changed their Xbox Live portal, forcing me to resort to the Node.JS version mentioned above, and this framework was mothballed.</p>
    <dl>
      <dt>Built with</dt><dd>PHP5, MySQL</dd>
    </dl>
  </div>
  <div class="span4">
    <img src="/assets/images/portfolio/personal/awesome-toolset.png" >
  </div>
</div>

<hr>

<div class="row-fluid portfolio-row">
  <div class="span4">
    <img src="/assets/images/portfolio/personal/caffeine-junkies-small.png" >
  </div>
  <div class="span8">
    <h3>Caffeine Junkies <small>http://www.caffeine-junkies.com/ (defunct)</small></h3>
    <p>My first full CMS build for a personal site I put together at school. Architecture based loosely off PHP Nuke, included forums, project management, and basic content modules to achieve just about any purpose. Remained main focus of personal coding projects for several years before hackers took down my server after mention on SlashDot.</p>
    <dl>
      <dt>Initial Build</dt><dd>Early 2002</dd>
      <dt>Built with</dt><dd>PHP4, MySQL</dd>
    </dl>
  </div>
</div>
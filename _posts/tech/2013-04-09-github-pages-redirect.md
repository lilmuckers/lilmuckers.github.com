---
layout: post
category : tech
title : "GitHub Pages - Jekyll Redirects"
tagline: "I'm keeping my traffic"
tags : [github, pages, jekyll, javascript]
summary: "Making sure that people who are looking for old content can find it on your new Jekyll site"
image: /assets/images/posts/github-pages-redirect/header.png
---
{% include JB/setup %}

## Introduction
Well, we've all been there, we've written a site, and one page is getting significantly more traffic than another, but several years after creating it you want to rewrite how your site works, but people still want the old content.

On my old site I wrote an article about [Magento Developement] [magento-part1], and that drew the bulk of my traffic. Which was odd because I never wrote a part 2 (well, I never *published* a part 2 anyway) so I didn't quite get that. "Oh Well.." I thought, whilst I merrily started rebuilding my site to use Jekyll on GitHub pages, not really caring that I'd lose this chunk of traffic.

This morning I realised that I was now giving people a lot of [404] [404] messages because of not considering this, and my Google Analytics told me just how many people were hitting the dead link and then not continuing. This is not what I wanted, I want people to read my site and suchlike.

---

### Research

I found a number of [articles] [redirect-jekyll] that talked about creating redirects to handle things like this, however they practically all required me to put in a plugin. As I'm sure you're aware GitHub Pages doesn't allow custom plugins outside of the default Jekyll set to be run on their servers. This is completley understandable, but is a bit of an arse. This meant that there was no clear and easy way to proceed on this front.

---

### Implementation
However, all was not lost, I hit upon an idea. It's hardly a perfect idea, because it doesn't send the appropriate header codes to search engines to notify them of the redirect, but it does at least mean that users can see the article they were looking for.

#### YAML Front Matter
Within the YAML Front Matter of the post that I was writing I added the link as it was on the old site as the parameter `oldurl`:
{% highlight yaml linenos %}
---
layout: post
category : magento
title : "Magento Development Guide - Part 1"
tagline: "Conciliation"
tags : [tutorial, magento, php, best practices]
summary: "Basic concepts a good magento developer should follow"
image: /assets/images/posts/magento-part-1/header.png
oldurl: /2011/02/a-guide-to-magento-development-part-1-conciliation/
---
{% endhighlight %} 

#### 404 Page
Within the `404.html` page I added a post iterator and a bit of javascript to actually do the redirect:
{% highlight html linenos %}
{% raw %}
<script type="text/javascript">
  var forwardLinks = []
  {% for post in site.posts %}	
  	{% if post.oldurl %}
  	  forwardLinks.push({ "old":"{{post.oldurl}}", "new":"{{post.url}}" })
  	{% endif %}
  {% endfor %}
  jQuery(function($)
  {
    //get the current pathname
    var currentPath = window.location.pathname
    
    //compare to the list
    for(var key in forwardLinks){
      var link = forwardLinks[key]
      if(link.old == currentPath){
        //set the redirect
        setTimeout("window.location.replace('"+link.new+"')", 5000)
        return
      }
    }
  })
</script>
{% endraw %}
{% endhighlight %}

This script simply loaded the redirects into an array, and then used javascript to push the user to the appropriate page depending on what the URL was that caused the 404.

---

### Conclusion

This is far from an ideal solution, but it's the best I could come up with that would allow me to keep using the Jekyll generation that sits within GitHub Pages.


[magento-part1]: {% post_url 2013-04-09-magento-part-1 %} "Magento Development Part 1"
[404]: /404.html "404- Dead Link"
[redirect-jekyll]: http://www.marran.com/tech/creating-redirects-with-jekyll/ "Creating Redirects With Jekyll"
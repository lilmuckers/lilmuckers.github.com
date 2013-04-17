---
layout: post
category : tech
title : "Xbox Live Tracker"
tagline: "Keeping track of the games."
tags : [jekyll, xbox, github, api, node.js]
summary: "How I built automated content functionality for Jekyll"
image: /assets/images/posts/xbox-live-tracker/header.png
---

## Introduction
One of the things I enjoy doing in my free time is playing games on my Xbox. Now, you might not find that fact all that surprising, given that pretty much the only remotely significant thing that is on this site at the moment is the [Xbox Tracker] [xbox-tracker], and that most of my posts in the last week have made mention of the tracker. 

Anyway, I thought I'd bring it all together to talk about what I did and how I did it, for two reasons really. So I had something to write a post about, and because I found writing it really really interesting. 

---

## Plan
I have, in the past, built a [node.js] [nodejs] based API so that myself, and some of my select friends, can directly query Xbox Live achievement data without having to go through the rigomorole of handling the login and html scraping of the ruefully awkward [Xbox Live site] [xbox-live]. It's a simple wrapper that uses [Phantom JS] [phantom-js] (and [Casper JS] [casper-js]) to log in to the site, load the appropriate page, retrieve the HTML, and then pull the appropriate data from it, and return it all as a **JSON** string.

So, my plan was to utilise the data from this API, to automatically update files on GitHub using the [API to commit the data] [blog-api-commits]. This did, however, drive my requirement to build a [Jekyll] [jekyll] website that would be compiled by the version of **Jekyll** available on the [GitHub pages] [gh-pages] platform, and therefore leave me somewhat limited as to the functionality I could include on the site.

The basic logic I wanted to follow was:

![Xbox Tracker Logic](http://www.websequencediagrams.com/files/render?link=RkpVYVvnf68nntKELlcr "Xbox Tracker Logic")

---

## Implementation

Most of it was pretty straight forward, really, but the biggest hurdle was building the **Jekyll** pages such that the game pages would be generated appropriately. I didn't want to get to the point of having a hardcoded page layout within the code, or some kind of template that was external from my Jekyll site. Mainly because this meant that it would be hard to backdate any changes to the templates, plus it means that any data would be difficult to migrate to a new system. I also wanted the data to be easy for the **Tracker** to read. Afterall, it would have to read it in some manner to get the scores and achievements ready for comparison.

### Reading From GitHub

Handily, the GitHub API provides a nice method to read a file from a repository - assuming you know the path to the file you want to read. And this works fine for the overview page, as we can set where that's generated. In my case it would be within `games/xbox.md`.

The API methods I use for this is the [get contents] [github-get-contents] call - and that's pretty straight forward. The reason that I expplicitly state `master` branch is because I've built this code to be repo agnostic - and I want to be able to use the collections of libraries I built to potentially power a collection of Jekyll sites, using `gh-pages` setup, and so forth.

{% highlight javascript %}
//using the githubber module
var GitHub = require('githubber')
 
//instantiate the Githubber github objects
var ghObj = new GitHub.GitHub()
var github = new GitHub.GitHubAPI(ghObj, 'accessToken')

//get contents by reference
github.repos.contents.refGet('lilmuckers', 'lilmuckers.github.com', 'games/xbox.md', 'master' function(err, data){
  //do something with the data
})
{% endhighlight %}

Getting the data from the individual game pages is a bit more involved, because we need to load a bunch of pages. Fortunately this is pretty simple with the GitHub API using a combination of the above mentioned [get contents] [github-get-contents] call and the [get recursive tree] [github-tree-recursive] call. However this requires a tree hash, so this is a 3 step process:

{% highlight javascript %}
//using the githubber module
var GitHub = require('githubber')
 
//instantiate the Githubber github objects
var ghObj = new GitHub.GitHub()
var github = new GitHub.GitHubAPI(ghObj, 'accessToken')

github.git.refs.info('lilmuckers', 'lilmuckers.github.com', 'heads/master', function(err, data){
  //now we have the SHA for the tree
  var treeSha = data.object.sha
  
  //now get the tree recursively
  github.git.trees.infoRecursive('lilmuckers', 'lilmuckers.github.com', treeSha, function(err, data){
    //build an array of the file paths that we want
    var returnArray = []
    
    //look at all paths and add it to the array
    for(var key in data.tree){
      var blob = data.tree[key]
      if(blob.type == 'blob' && blob.path.indexOf('the/path/we/want') == 0){
        returnArray.push(blob)
      }
    }
  })
})
{% endhighlight %}

### Slugs

I needed a way to uniquely identify any game, and any achievement if I was to reliably to any kind of matching between what was stored within GitHub and what was being returned by the Xbox Live scraper. Towards this end I wrote a very simple `String.slugify()` function, that would generate slugs for games and achievements in such a way that I could count on the consistency of the output. What we wanted to do was convert all the potential multi-byte characters either into non-entities (for example **&trade;** and **&copy;** aren't really required for a game name) or into meaningful representations of themselves (**&uuml;** into `ue`,  **&amp;** into `and`, and so forth) so that the slugs would still make sense in the context of the games and achievements they were representing.
This also ensured that the identification strings would be in `UTF-8` format, which is much simpler to deal with, and allows me to easily create filenames that make sense as URLs.

<script src="https://gist.github.com/lilmuckers/5398242.js"> </script>

So games are a slug of the gamename - and the achievements have a slug of the game name concatonated with a slug of the achievement name. So, for example, **Lego Lord of The Rings** ([game page] [lego-lotr-gamepage])  is identified on the achievement page as **Lego LOTR** - meaning its slug is `lego-lotr`. The achievement "**This is no mine... it's a tomb.**" has the slug `lego-lotr_this-is-no-mine--its-a-tomb`.

### YAML Front Matter
I realised that the [YAML Front Matter] [yaml-front-matter] of the pages could be used to this end pretty effectively, and using the layouts stored within the `_layouts` directory to act as renderer for that data. This meant that, handily, all the design and layout for the data from Xbox Live would be kept as an internal part of **Jekyll**.

I always try to write any code, even stuff that only I am ever to use, in such a way that in theory it's completely independant to what I require - and the final stuff that's specific to me is handled by configuration, or at the final presentation layer. I find staying in that mindset helps me stay pretty on the ball in any project.

I used [js-yaml] [js-yaml] to both parse and serialise the YML from the Front Matter for use within the **node.js** code. This meant that once I had loaded the page content from **GitHub** by the API, I could extract and load the YML into a JSON object with something as simple as (it's worth noting that in the code I implemtented there was more error checking - just in case):
{% highlight javascript %}
var items = content.split('---');
try{
  var returnData = {
    path: path,
    info: yaml.load(items[1]),
    content: items[2]
  }
} catch(e){
  var returnData = {
    path: path,
    info: {},
    content: items[2]
  }
}
{% endhighlight %}

Thus leaving me with the page content, and the YAML handily seperated, along with the path that the file had (allowing me to ensure that when I saved modifications back to GitHub they would overwrite the old file correctly). This meant that any modifications to the file outside of the **Tracker** would be preserved. For example, if I wanted to add some MarkDown to talk about the game page, it would be there, and the page would just be updated with new achievements.

### Overview Page

I wanted the xbox specific to be contained in a single variable within the YAML, and for it to store as much data as is possible - just so that when I came to present the data, I would have as much to play with as I wanted. As you can see, however, from the page itself - I didn't end up using all the data, but it did give me flexibility when it came to the design itself. I also kept a list of the 20 latest achievements, allowing me to show such a list on my homepage, or on the overview page - just to make things a bit nicer.

{% highlight yaml %}
---
layout: xbox
title: Xbox Tracker
tagline: "I play games, they are fun"
group: games
achievementcount: 1373
gamer: 
  gamertag: Lilmuckers
  avatar: 
    secure: "https://avatar-ssl.xboxlive.com/avatar/Lilmuckers/avatar-body.png"
    unsecure: "http://avatar.xboxlive.com/avatar/Lilmuckers/avatar-body.png"
  activity: Last seen 11/04/2013 playing Xbox Dashboard
  picture: 
    small: "http://avatar.xboxlive.com/avatar/Lilmuckers/avatarpic-s.png"
    large: "http://avatar.xboxlive.com/avatar/Lilmuckers/avatarpic-l.png"
  score: 25890
  location: Londinium
  motto: "meritocracy!"
  bio: "ARGH! BEES!"
  profile: "http://live.xbox.com/en-GB/Profile?gamertag=lilmuckers"
gamecount: 101
latest: 
 - image: "https://live.xbox.com/tiles/Jp/CD/04CLiGJhbC9DAhoEGlFTVmQxL2FjaC8wLzg3AAAAAOfn5-yskDo=.jpg"
   name: Hot Pursuit
   description: You completed 5 pursuits in the Bay Area
   score: 15
   acquired: "2013-04-10"
   slug: "defiance_hot-pursuit"
   gameSlug: defiance
   scraped: "Wed Apr 10 2013 17:25:56 GMT+0100 (BST)"
 - {...}
---
{% endhighlight %}

### Game pages
The game pages were going to require a bit more thought, as I can't really hardcode for them within the templates, so I needed to use an idea already extant within [Jekyll Bootstrap] [jekyll-bootstrap] which is to add an attribute of "group" so that I can iterate through all the game pages, and only display the ones that are games (along with some other conditionals that I don't want to go through at this point).

{% highlight html %}
{% raw %}
{% for weight in (0..page.gamecount) reversed %}
  {% for game in site.pages %}
    {% if game.group == "xboxgame" %}
      {% if game.game.totalAchievements != 0 and weight == game.weight %}
        <h1>{{game.title}}</h1>
      {% endif %}
    {% endif %}
  {% endfor %}
{% endfor %}
{% endraw %}
{% endhighlight %}


---
## Bringing it all together
The best way I can think of to bring all elements of the build together in an easy to read format is with another web sequence diagram that explains all the logic that the system goes through (warning, this is pretty exhaustive):

![Full Xbox Tracker Logic](http://www.websequencediagrams.com/files/render?link=OjD4QvWLkNH8FGPqRiG2 "Full Xbox Tracker Logic")

---
## Conclusion
This was actually a really fun and interesting project to work on, and i can only hope I explained something in an understandable way. I've gone into the main challenges that I worked through, along with a sequence diagram to explain the full logic on how the system works, and hopefully that is understandable.
It turns out that using the GitHub API and making sure my Jekyll site can be compiled by GitHub Pages itself means that I can add some really nice bits of "dynamic" functionality to my site without having to worry about backend code securities, or even hosting. Plus it means that *everything* on my site is versioned, including all the achievement updates I get.
This means GitHub can tell my how much time i'm spending on my Xbox, so I can feel guilty when I see it on day to day work hours...

[xbox-tracker]: /games/xbox.html "Xbox Live Achievement Tracker"
[nodejs]: http://nodejs.org/ "Node.js"
[xbox-live]: http://www.xbox.com/ "Xbox Live"
[phantom-js]: http://phantomjs.org/ "Phantom JS Headless Webkit Browser"
[casper-js]: http://casperjs.org/ "Casper JS navigation and scripting utility"
[blog-api-commits]: {% post_url 2013-04-08-github-api-commit %} "GitHub API Commits"
[jekyll]: http://jekyllrb.com/ "Jekyll HTML Site Builder"
[gh-pages]: http://pages.github.com/ "GitHub Pages"
[yaml-front-matter]: https://github.com/mojombo/jekyll/wiki/YAML-Front-Matter "Jekyll YAML Front Matter"
[js-yaml]: https://github.com/nodeca/js-yaml "YAML 1.2 parser and serializer for JavaScript"
[lego-lotr-gamepage]: /games/xbox/lego-lotr.html "Lego Lord Of The Rings Xbox Live Achievement Tracker"
[github-get-contents]: http://developer.github.com/v3/repos/contents/#get-contents "GitHub Get Contents API Call"
[github-tree-recursive]: http://developer.github.com/v3/git/trees/#get-a-tree-recursively "GitHub Get Tree Recursively"
[jekyll-bootstrap]: http://jekyllbootstrap.com/ "Jekyll Bootstrap"

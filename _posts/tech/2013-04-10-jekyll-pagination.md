---
layout: post
category : tech
title : "Jekyll Pagination"
tagline: "More awkward than it needs to be"
tags : [jekyll, pages]
summary: "Getting pagination working on Jekyll is more awkward than it needs to be"
image: /assets/images/posts/jekyll-pagination/header.png
---
{% include JB/setup %}

## Introduction
I had this weird requirement to have seperate pagination templates that are different from my homepage. I know, weird right? No it's true!
It turns out that Jekyll pagination is pretty simplified, and whilst it offers a certain level of flexibility, it's flexibility that doesn't make any sense.

The pagination guide within the [jekyll documentation] [jekyll-pagination] is pretty complete, however it fails to mention that it expects you to use the `/index.html` file as the template for generating all pagination templates, and that `/index.html` would be **page 1**. Since I didn't want that functionality exactly, I came up with another solution.

---

## Problem

The way that Jekyll generates the pagination by default is to first enter in the pagination configuration in `_config.yml`
{% highlight yaml %}
paginate: 10
{% endhighlight %}

And then it will generate pagination, using `/index.html` as the template, within the site paths of `/page2/`, `/page3/`, `/page4/` and so forth, using `/index.html` as the **page 1**.

However, as you can tell from [my pagination] [my-pagination] this wasn't the behaviour I wanted, but i did find a way around it.

---

## Solution

There is a little documented option in Jekyll that I found in an old [pull request] [pagination-root-pull-request] that added the config option of `paginate_path` to the scope. This allows you to specify the path in which the paginated pages should be generated, using `:num` to represent the actual number. It will still generate them as directories, so hoping for nice `.html` extensions is a bit much.

So I entered:
{% highlight yaml %}
paginate: 10
paginate_path: posts/page:num
{% endhighlight %}

I had created a specific template for pagination within `posts/index.html`, as I had found out that the `pagination` site variable was only available on templates with `index.html` as the filename.

So, now I had the first page being generated, and all the other pages were being generated off the root `index.html` template, which was not what I wanted.

My solution to this was a little hacky, and was to create a partial in `_includes/custom/pagination` and include that in the pagination template using:

{% highlight html %}
{% raw %}
{% include custom/pagination %}
{% endraw %}
{% endhighlight %}

And finally enter into the root `/index.html` an if statement that meant if it thought it was being generated in the scope of a `page2`, `page3`, etc - it would include the partial template:


{% highlight html %}
{% raw %}
{% if paginator.page != 1 %}
  {% include custom/pagination %}
{% else %}
<div class="page-header">....
{% endif %}
{% endraw %}
{% endhighlight %}

And that was it, awkward, but at least it does what I want it to do now.

---

## Conclusion

As much as I'm liking Jekyll, it honestly feels like I'm engaged in some kind of extended boxing match with it, trying to outwit it. Now, granted, there are many plugins available that will make Jekyll sing and dance and do what I want it to do, as well as being written in ruby so that i could write plugins myself, but this isn't an option when you're hosting on GitHub Pages - unless you want to generate the site prior to pushing to github, and that's not an option for me.


[jekyll-pagination]: https://github.com/mojombo/jekyll/wiki/Pagination "Jekyll Pagination Documentation"
[my-pagination]: /posts/ "My Pagination"
[pagination-root-pull-request]: https://github.com/mojombo/jekyll/pull/342 "Pagination 'paginate_path' pull request"
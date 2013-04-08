---
layout: post
category : tech
title : "GitHub API Commits"
tagline: "I'm not afraid of committing, I automate it..."
tags : [github, api, javascript, node.js]
summary: "Writing commits to GitHub via the API isn't all that straight forward"
image: /assets/images/posts/github-api-commit/header.png
---
{% include JB/setup %}

## GitHub API

As you might have guessed, I rather like the GitHub API. Especially lately, I've been using it a lot to accomplish various tasks within both my professional and personal work, and one of the obstacles I've come across is the need to commit to a repository, and it not being entirely clear how to do that without using direct git bindings on your server.

### So why not use git bindings?

Well, I tried that, that was my first exploration into this, unfortuantely I encountered many issues with this. Not least because my chosen language for a lot of my automated processes is [node.js] [nodejs] and there are precious few bindings that I liked the look of.
The main one I would consider using is [Gitteh] [gitteh], however it seems that installing it on any of my servers throws up different errors every single time. Seriously. I was going spare. They were known bugs as well, so i figured they'd get fixed. I waited for a while, watching bugfixes get committed to the project, however new problems cropped up and I just decided to have a go at another solution.

### What advantages does using the GitHub API have?

Well, the main thing is that it's completely platform agnostic, all you need is the ability to sent **https** based requests to a server, and you don't need to have the git repo on your local system to interact with it. It's a lot simpler, and in all honesty I don't know why I didn't go for it from square one...

## Commits

Unfortunately, as complete as the GitHub API is when it comes to the [git data] [git-data] calls to interact with your repository you kinda need to understand the inner workings of [git itself] [git]. I'm a web developer, that's not something I've ever really had cause to research. 

After a fair amount of googling, reading of the above [git documentation] [git-internals], and a lot of trial and/or error I hit upon an efficient and reliable way to do it in a simple 5 step process. I'll document my process for doing commits using the GitHub API, and then show a [node.js] [nodejs] based helper I built for my applications to make it even easier.

Obviously you'll need an [oAuth access token] [github-oauth] with at least `repo` scope access that covers your repository. You can generate one for yourself by using [my oAuth token tool] [oauth-token-tool].

### Process

#### 1. Get the SHA of the latest commit on the branch

Step 1 is to get the `SHA` of the latest commit on the target branch. To get that we need to get data about the `reference` through the API. The api documentation for this is [here] [ref-get-info], but in short, if I were doing this with **cURL** the command, and response, would be (remember that the reference name needs to be a full reference, like `heads/master`:

{% highlight bash %}
 $ curl -i https://api.github.com/repos/lilmuckers/api-commits/git/refs/heads/master?access_token=<access_token>
 
 HTTP/1.1 200 OK
 Content-Type: application/json; charset=utf-8
 Connection: keep-alive
 Status: 200 OK
 X-RateLimit-Limit: 5000
 X-RateLimit-Remaining: 4999
 Content-Length: 333
 
 {
   "ref": "refs/heads/master",
   "url": "https://api.github.com/repos/lilmuckers/api-commits/git/refs/heads/master",
   "object": {
     "sha": "544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a",
     "type": "commit",
     "url": "https://api.github.com/repos/lilmuckers/api-commits/git/commits/544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a"
   }
 }
{% endhighlight %}

The `SHA` you're looking for is at the `object.sha` point in the response. I shall now call this the `initialCommitSha`.

#### 2. Get the tree information for that commit

Step 2 is that we need to get the tree `SHA` for that commit. The api documentation for this api command is [here] [commit-get-info], and here's the cURL command, using the `initialCommitSha` from **Step 1**. Or, if you like, you can use the `object.url` as the API endpoint.

{% highlight bash %}
 $ curl -i https://api.github.com/repos/lilmuckers/api-commits/git/commits/544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a?access_token=<access_token>
 
 HTTP/1.1 200 OK
 Content-Type: application/json; charset=utf-8
 Connection: keep-alive
 Status: 200 OK
 X-RateLimit-Limit: 5000
 X-RateLimit-Remaining: 4998
 Content-Length: 786
 
 {
   "sha": "544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a",
   "url": "https://api.github.com/repos/lilmuckers/api-commits/git/commits/544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a",
   "html_url": "https://github.com/lilmuckers/api-commits/commits/544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a",
   "author": {
     "name": "Patrick McKinley",
     "email": "contact@patrick-mckinley.com",
     "date": "2013-04-08T13:48:40Z"
   },
   "committer": {
     "name": "Patrick McKinley",
     "email": "contact@patrick-mckinley.com",
     "date": "2013-04-08T13:48:40Z"
   },
   "tree": {
     "sha": "ccdb357ec4e028944741c52f906a7f05ab602d79",
     "url": "https://api.github.com/repos/lilmuckers/api-commits/git/trees/ccdb357ec4e028944741c52f906a7f05ab602d79"
   },
   "message": "Initial commit",
   "parents": [
 
   ]
 }
{% endhighlight %}

The `SHA` you're looking for here is in the `tree.sha` point in the response. I shall call this the `initialTreeSha`.

#### 3. Create a new tree for your commit

The next step is to create a new tree containing the changes that you want to push to the repository, this would be a `POST` request referencing the `initialTreeSha` as `base_tree` and an array of hashes representing the details for the file changes you're making. The documentation for this API request is [here] [create-tree]. The `path` property on the file hash can be a full path with folders and suchlike.

This request body would be something like:
{% highlight json linenos %}
{
  "base_tree": "ccdb357ec4e028944741c52f906a7f05ab602d79",
  "tree": [
    {
      "path": "test_file.txt",
      "mode": "100644",
      "type": "blob",
      "content": "this is the content of the file"
    },
    {
      "path": "folder/test_file.txt",
      "mode": "100644",
      "type": "blob",
      "content": "This file is in a folder"
    }
  ]
}
{% endhighlight %}

This is sent using a request like:

{% highlight bash %}
 $ curl -i -d '{"base_tree": "ccdb357ec4e028944741c52f906a7f05ab602d79","tree": [{"path": "test_file.txt","mode": "100644","type": "blob","content": "this is the content of the file"},{"path": "folder/test_file.txt","mode": "100644","type": "blob","content": "This file is in a folder"}]}' https://api.github.com/repos/lilmuckers/api-commits/git/trees?access_token=<access_token>
 
 HTTP/1.1 201 Created
 Content-Type: application/json; charset=utf-8
 Connection: keep-alive
 Status: 201 Created
 X-RateLimit-Limit: 5000
 X-RateLimit-Remaining: 4997
 Content-Length: 1007
 
 {
   "sha": "b72a4521d76b541429ab9bd8aeae4b4d41bb5534",
   "url": "https://api.github.com/repos/lilmuckers/api-commits/git/trees/b72a4521d76b541429ab9bd8aeae4b4d41bb5534",
   "tree": [
     {
       "mode": "100644",
       "type": "blob",
       "sha": "4df5feb30274fd4d451c390f969df52d46ddcf8f",
       "path": "README.md",
       "size": 44,
       "url": "https://api.github.com/repos/lilmuckers/api-commits/git/blobs/4df5feb30274fd4d451c390f969df52d46ddcf8f"
     },
     {
       "mode": "040000",
       "type": "tree",
       "sha": "09b7be25ca5f1ecff862a33fe29a6aa9df9f2cbb",
       "path": "folder",
       "url": "https://api.github.com/repos/lilmuckers/api-commits/git/trees/09b7be25ca5f1ecff862a33fe29a6aa9df9f2cbb"
     },
     {
       "mode": "100644",
       "type": "blob",
       "sha": "b92840c5d8cc2c72d5aef6c297ecb7cff9cc6c93",
       "path": "test_file.txt",
       "size": 31,
       "url": "https://api.github.com/repos/lilmuckers/api-commits/git/blobs/b92840c5d8cc2c72d5aef6c297ecb7cff9cc6c93"
     }
   ]
 }
{% endhighlight %}

The response sent back has details of the `tree` that was created, and the key bit of information in the response is the `sha` property of the JSON. I shall refer to this as `newTreeSha` from here.

#### 4. Create the commit
Step 4 is to create a commit to describe this tree. The documentation for this is [here] [create-commit]. For this you need to set its parent to be the `initialCommitSha` from **Step 1** as an array in the `parents` property, and you need to link it to the `newTreeSha` from **Step 3** as the `tree` property. This is also where you would enter the commit message.

The request body would be something like:
{% highlight json %}
{
  "message": "This is a test commit",
  "tree": "b72a4521d76b541429ab9bd8aeae4b4d41bb5534",
  "parents": ["544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a"]
}
{% endhighlight %}

And you would send this data with a request like
{% highlight bash %}
 $ curl -i -d '{"message": "This is a test commit","tree": "b72a4521d76b541429ab9bd8aeae4b4d41bb5534","parents": ["544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a"]}' https://api.github.com/repos/lilmuckers/api-commits/git/commits?access_token=<access_token>
 
 HTTP/1.1 201 Created
 Content-Type: application/json; charset=utf-8
 Connection: keep-alive
 Status: 201 Created
 X-RateLimit-Limit: 5000
 X-RateLimit-Remaining: 4996
 Content-Length: 1093
 
 {
   "sha": "7fb6501ce53e92035220ed0f3f4bb1406ca5b587",
   "url": "https://api.github.com/repos/lilmuckers/api-commits/git/commits/7fb6501ce53e92035220ed0f3f4bb1406ca5b587",
   "html_url": "https://github.com/lilmuckers/api-commits/commits/7fb6501ce53e92035220ed0f3f4bb1406ca5b587",
   "author": {
     "name": "Patrick McKinley",
     "email": "contact@patrick-mckinley.com",
     "date": "2013-04-08T16:08:06Z"
   },
   "committer": {
     "name": "Patrick McKinley",
     "email": "contact@patrick-mckinley.com",
     "date": "2013-04-08T16:08:06Z"
   },
   "tree": {
     "sha": "b72a4521d76b541429ab9bd8aeae4b4d41bb5534",
     "url": "https://api.github.com/repos/lilmuckers/api-commits/git/trees/b72a4521d76b541429ab9bd8aeae4b4d41bb5534"
   },
   "message": "This is a test commit",
   "parents": [
     {
       "sha": "544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a",
       "url": "https://api.github.com/repos/lilmuckers/api-commits/git/commits/544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a",
       "html_url": "https://github.com/lilmuckers/api-commits/commits/544aed9214ab1fd7b83bad5ebfac5b9bacf1c78a"
     }
   ]
 }
{% endhighlight %}

The key bit of information is the `sha` property on the response, as this is the commit `sha`. I shall refer to this as `newCommitSha`.

#### 5. Link commit to the reference
The next, *and final*, step is to update the `reference` to refer to this new `commit`. You'll only need to send the `reference` label from **Step 1** in the format of `heads/master`, and the `newCommitSha` from **Step 4**. The documentation for this api call is [here] [update-ref]. Remember it needs to use a **HTTP Verb** of `PATCH`.

The request body would be something like:
{% highlight json %}
{
  "sha": "7fb6501ce53e92035220ed0f3f4bb1406ca5b587"
}
{% endhighlight %}

And you'd send a request similar to:
{% highlight bash %}
 $ curl -i -X PATCH -d '{"sha": "7fb6501ce53e92035220ed0f3f4bb1406ca5b587"}' https://api.github.com/repos/lilmuckers/api-commits/git/refs/heads/master?access_token=<access_token>
 
 HTTP/1.1 200 OK
 Content-Type: application/json; charset=utf-8
 Connection: keep-alive
 Status: 200 OK
 X-RateLimit-Limit: 5000
 X-RateLimit-Remaining: 4995
 Content-Length: 333
 
 {
   "ref": "refs/heads/master",
   "url": "https://api.github.com/repos/lilmuckers/api-commits/git/refs/heads/master",
   "object": {
     "sha": "7fb6501ce53e92035220ed0f3f4bb1406ca5b587",
     "type": "commit",
     "url": "https://api.github.com/repos/lilmuckers/api-commits/git/commits/7fb6501ce53e92035220ed0f3f4bb1406ca5b587"
   }
 }
{% endhighlight %}

#### 6. Enjoy
You've now successfully committed a change to your repository, I suggest you make a cup of tea and relax. Unfortuantely GitHub don't have an API call to make tea, but I'm sure some tea making machines have one...

You can view your change on GitHub now, and you can see the change I made whilst writing this article [here] [github-api-commit-link].

## Why?

Well, I've been wanting to do this kind of thing for some time in a professional capacity, use GitHub has a way to version reports built by reporting systems, and in a personal use - for example, I use this stuff for keeping by [Xbox Live Tracker] [xbox-live] up to date. I also want to add more things in the future that keep this Jekyll website up to date. So it's pretty handy.

## Code
This whole process is pretty annoying complicated, so I wrote a **node.js** script to do the whole thing for me, that runs off my [githubber] [githubber] library. there is a usage example as a part of the gist.

<script src="https://gist.github.com/lilmuckers/5328532.js">    </script>


[nodejs]: http://nodejs.org/ "Node.JS"
[gitteh]: https://github.com/libgit2/node-gitteh "Gitteh libgit2 bindings for node.js"
[git-data]: http://developer.github.com/v3/git/ "Git Data GitHub API Documentation"
[git]: http://git-scm.com/ "Git Homesite"
[git-internals]: http://git-scm.com/book/en/Git-Internals "Git internals Documentation"
[github-oauth]: http://developer.github.com/v3/oauth/ "GitHub API oAuth"
[oauth-token-tool]: {% post_url 2013-04-07-github-api-oauth %} "GitHub API oAuth generation tool"
[ref-get-info]: http://developer.github.com/v3/git/refs/#get-a-reference "GitHub API Documentation - get a reference"
[commit-get-info]: http://developer.github.com/v3/git/commits/#get-a-commit "GitHub API Documentation - get a commit"
[create-tree]: http://developer.github.com/v3/git/trees/#create-a-tree "GitHub API Documentation - create a tree"
[create-commit]: http://developer.github.com/v3/git/commits/#create-a-commit "GitHub API Documentation - create a commit"
[update-ref]: http://developer.github.com/v3/git/refs/#update-a-reference "GitHub API Documentation - update a reference"
[github-api-commit-link]: https://github.com/lilmuckers/api-commits "Link to my API Commits repo"
[githubber]: https://npmjs.org/package/githubber "node.js GitHub API bindings"
[xbox-live]: /games/xbox.html "Xbox Live Achievement Tracker"

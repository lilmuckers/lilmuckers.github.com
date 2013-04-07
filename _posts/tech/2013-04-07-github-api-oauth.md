---
layout: raw
category : Tech
title : "GitHub API oAuth"
tagline: "For your command line helpers"
tags : [github, api, oauth, javascript]
summary: "Sometimes it can be a real pain to get GitHub oAuth sorted out"
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
      <h2>Authorisations Tool</h2>
      <p>Enter in your username, password, and the scopes you want the oAuth token to cover, along with a note so that you know what this oAuth is for, y'know, it's handy. I can assure that this code does not save the password anywhere and merely uses it to log into GitHub and set up a new authorisation token. If you want to check it out, the code for this is in the gist below (I'm using the gist as the hosting for the script too, just so you can be 100% sure there's no funny business).</p>
    </div>
  </div>

  <div class="span6">
    <form id="ghAuthApi" class="form-horizontal">
      <fieldset>
        <legend>GitHub Authorisations API</legend>
        <div class="control-group">
          <label class="control-label" for="inputUsername">Username</label>
          <div class="controls">
            <input name="username" type="text" id="inputUsername" class="input-medium" placeholder="GitHub Username" />
          </div>
        </div>
        <div class="control-group">
          <label class="control-label" for="inputPassword">Password</label>
          <div class="controls">
            <input name="password" type="password" id="inputPassword" class="input-medium" placeholder="GitHub Password" />
          </div>
        </div>
        <div class="control-group">
          <label class="control-label" for="inputNote">Note</label>
          <div class="controls">
            <input name="note" type="text" id="inputNote" class="input-medium" placeholder="Note" />
          </div>
        </div>
        <div class="control-group">
          <label class="control-label" for="selectScopes">Scopes</label>
          <div class="controls">      
            <select name="scopes" id="selectScopes" multiple="multiple" >
              <option value="">(no scope)</option>
              <option value="user">user</option>
              <option value="user:email">user:email</option>
              <option value="user:follow">user:follow</option>
              <option value="public_repo">public_repo</option>
              <option value="repo">repo</option>
              <option value="repo:status">repo:status</option>
              <option value="delete_repo">delete_repo</option>
              <option value="notifications">notifications</option>
              <option value="gist">gist</option>
            </select>
          </div>
        </div>
        <div class="control-group">
          <div class="controls">      
            <button type="submit" class="btn">Get My oAuth!</button>
          </div>
        </div>
      </fieldset>
    </form>
  </div>
  <div class="span6">
    <script src="https://gist.github.com/lilmuckers/5331280/raw/oauth.js">   </script>
  
    <form id="ghAuthApiResponse" class="form-horizontal">
      <fieldset>
        <legend>GitHub Authorisations Response</legend>
        <div class="alert alert-block alert-success" style="display: none;">
          <h4>Success!</h4>
          <dl>
            <dt>oAuth Token</dt>
              <dd id="oauthToken"> </dd>
            <dt>Note</dt>
              <dd id="oauthNote"> </dd>
            <dt>Created</dt>
              <dd id="oauthCreated"> </dd>
            <dt>Scopes</dt>
              <dd id="oauthScopes"> </dd>
          </dl>
        </div>
        <div class="alert alert-block alert-error" style="display: none;">
          <h4>Error!</h4>
          <p></p>
        </div>
        <div class="alert alert-block alert-info">
          <h4>Fill 'er in!</h4>
          <p>Fill in the form to the left, and we'll get right on the job.</p>
        </div>
      </fieldset>
    </form>
  </div>
  <div class="span12">
    <div class="content">
      <hr>
      <h2>The story...</h2>
      <p>In recent months i've had many occasions where I've needed to use the <a href="http://developer.github.com" alt="GitHub Developers API">GitHub API</a>, for both personal projects and under the aegis of stuff i've done for work. The main obstical I've had is that if you're building a command line tool, as I usually do for these things, there isn't a nice way to get an oAuth token, like there is for a web app.</p>
  
      <p>As I'm sure you're aware when you're using oAuth with a web app, you get forewarded to a nice GitHub login page where you accept/deny the request to allow the application access to your github account. This isn't really possible when what you're doing is a command line tool. Moreover when you're building a tool that is only going to be used by yourself, or by a small closed set of developers, you don't really need that added niceness layer, you just need the oAuth access token so you can get on with what you need to do.</p>
  
      <p>Fortunately GitHub have an API method that is just the solution to this, it's the <a href="http://developer.github.com/v3/oauth/#create-a-new-authorization" alt="GitHub Developers API - Authorisations">authorisations API</a>, where you can pass a username and a password to get an oAuth token in return, handily meaning you don't have to store the users password in some plain text.</p>
  
      <p>Here's the code for a <a href="http://flatironjs.org/" alt="Flatiron JS framework">Flatiron.js</a> command script that I have used to generate my oAuth tokens, it depends upon my <a href="https://npmjs.org/package/githubber" alt="Githubber Node.JS library">githubber</a> node.js github api library.</p>
  
      <script src="https://gist.github.com/lilmuckers/5331033.js">   </script>
  
  
      <p>As you can see; it's a pretty simple script.</p>
  
      <p>However it still requires having it set up on a server or my computer, and using the command line, and blargh - that just doesn't make me happy when it comes to a usability. Plus I figured that other people have a similar problem, and I wanted to try out some cool dynamic cross-server javascript coding on gitHub Pages.</p>
  
      <p>So. You know. There's that.</p>
      
      <hr>
      <h2>The Code...</h2>
      
      <script src="https://gist.github.com/lilmuckers/5331280.js">    </script>
      
    </div>
  </div>
  
  {% include custom/post_meta %}
  
</div>
---
layout: post
title:  "GitHub Oath, node + http"
date:   2015-06-19 18:05:42
tags: ["front"]
---

Let's face it. OAuth can be tricky. Luckily there are npm modules that can do most of the heavy logic. However understanding the basic oath procedure is key to successfully utilizing OAuth npm modules, as well as some understanding of pathnames, redirects, and regex.

Here's what I learned from implementing the github-oauth module:
https://www.npmjs.com/package/github-oauth,
along with basic required steps that are referred to but not explicitly explained.

First you must start a new developer application in the settings panel of your github account.
If your site is not deployed use the http://localhost:portname paths for homepage and authorization callback.

Next start a server with the `http` module, copy the code in the link, and remember to do
`npm install http` and `npm install request` as that is a dependency.

Here are the key personalizations you need to pay attention to:

1) gitHubClient variable: change process.env['GITHUB_CLIENT'] to the actual string 'client_id' provided to you in the developer application page on github. Do the same for githubSecret. These can later be changed to environment variables to keep them secert.

2) Set the baseURL to your localhost path and portname. Choose what path loginURI you want to use for example '/signin' and callbackURI '/success'. Scope can be set to an empty string.

3) Everything else stays the same.

{% highlight js %}
require('http').createServer(function(req, res) {
  if (req.url.match(/signin/)) return githubOAuth.login(req, res);
  if (req.url.match(/success/)) return githubOAuth.callback(req, res);
}).listen(8000)

{% endhighlight %}

The OAuth process works like this:

1) Your server redirects the client to the GitHub authorization page with the `githubOAuth.login` function. The client logs in on GitHub.

2) GitHub sends a redirect back to your site with the callbackURI, mine was '/success', a temporary code and the state parameter, a radom string added by the login function. The `/success/` req.url.match uses regex to identify request urls that have matching strings to the callbackURI.

3) The oauth-github module callback function sends a get request to github, this time with the githubClient key, the githubSecret, the code and the state. Github exchanges this information for an access token, which our callback request handler will emmit an event which triggers the githubOAuth.on('token') function to serve us the user token.

Path names can be finicky, so if errors such as redirect loops keep popping up or if you can't get past the sent code state to talk to gihub again for the token, I suggest chekcing out the network tab and carfeully looking at the urls, to see if they actually exist on the server you created. Also double check that the url request parameters sent to github have the proper variable names


The req.url matcher here uses regex `/success/` to find that string in the path name that github sends back with the code.



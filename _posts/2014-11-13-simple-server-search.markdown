---
layout: post
title:  "Search with a Simple Server"
date:   2014-11-13 18:05:42
tags: interview
categories: jekyll update
---
Here's a challenge that can be done in 20 min:

####Create a search suggest bar with a simple server. This search suggest should read the input prefix with jQuery, make an AJAX call, and return 10 words that begin with that prefix as the user types. 

OK, go!

I'm going to do this using Sinatra as a server for a simple internal call to dictionary file.


Create a directory for our html pages and server files. 
`mkdir name`
`cd name`
Create a file for our server code:
`touch sinatra.rb`
Create a views directory:
`mkdir views`
`cd views`
Add an erb file in views for our html:
`touch search.erb`
`cd ..`
We need a gemfile in the root folder:
`bundle init`
(if you don't have bundler yet: http://bundler.io/rationale.html)

In the gemfile that was created put this gem and source:
{% highlight ruby %}
source "https://rubygems.org"
gem ‘sinatra’
{% endhighlight %}
Then in the terminal:
`bundle install`
or
`gem install sinatra`

Test our server:

In the sinatra.rb file put:
{% highlight ruby %}
require 'sinatra'

get '/hi' do
  "Hello World!"
end
{% endhighlight %}

Save it!

Start the server in the terminal with:

`ruby sintatra.rb`

In the code that starts the server, check for the port number it is using-- ex: 3000, 4000, 4567
then in the browser go to http://localhost:port#/hi. If the server is working you should see “Hello World!” in the browser.

Great! Let's implement the search bar and the dictionary. 

Grab a dictionary from a file online:Here's one that works: [Dictionary][file]. Save it in the main directory, with a name such as words.txt, dictionary.txt...

Open your erb file in the views directory and let's write the html structure. We need an input tag, a button, and a div with an id where the AJAX call can spit back the search results. 

{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
	<script src="http://code.jquery.com/jquery-1.10.2.js"></script>
	<script>

	</script>
</head>
<form>
	<input name="search" type="text" size="10" ><button>Search</button>
</form>
<div id="search-suggest">
</div>
{% endhighlight %}

Now for the jQuery/AJAX, In the empty script put this (I'll explain it below!):

{% highlight html %}
$(function() {
	$('form input').on('keyup', searchSuggest);
		function searchSuggest(ev) {
			var q = $('form input').val()
			console.log(q);
			$('#search-suggest').load('http://localhost:4567/suggest?q=' + q);
			}
		});
{% endhighlight %}
We start with a jQuery function that waits for the "key up" stroke from the user in the "form input" search bar. Upon "key up", the SearchSuggest function will be executed. The SearchSuggest function saves the letter iputted but the user into a variable with the .val function on the form and input tags. We can log the q variable to the console just to make sure it was saved. Then  with the .load function on the div where we want our results (id=search-suggest), we are going to perform a "get" request to the server, with our user input varible. Remeber the port in the http request should be the one your sinatra server is using. However we haven't written the definition of our /suggest/ request yet!

Let's ask the server to render our html page, then write the "get" request for the dictionary.

Open the ruby server file, (we saved it as sinatra.rb)
Write a get request definition with the name of the erb file to open (we called ours search.erb)  

{% highlight ruby %}
get '/search' do
  erb :search
end
{% endhighlight %}

Now make sure the server is running (`ruby sinatra.rb`) and go to: http://localhost:4567/search 

You should see your search bar with the button! Type something in the bar; it doesn't work yet :(

Ok, let's write the /suggest/ AJAX, get request that we put in our script. I'm going to put it here with #comments for explanation. 

{% highlight ruby %}
WORDS = File.readlines('dictionary.txt').freeze
#upon starting the server, Sinatra will have the dictionary loaded in memory
(make sure the .txt file is where you saved your word list)
#freeze is a Ruby object method that prevents further modification

get '/suggest' do
  q = params[:q]
# :q are the words the user is typing in, which we will save in q again.
  results  = []
# start an array to put the result list
  re = /^#{ q }.*/
# a regular expression, used to check whether the words in the dictionary match the user input, 'q'
  s = Time.now
# let's use this to check how long it takes to find 10 words.
# iterate through the dictionary:
  WORDS.each do |w|
  	break if results.length == 10 
  	if !(w =~ re).nil?
  	  results << w
  	end
  end
  
  results.map { |r| "<div>#{ r }</div>"}.join('') + "<div>#{ Time.now - s }</div>"
  #map the results as a formatted array, and the difference in time as output. 
End
{% endhighlight %}

Ok, now if your request address in your script is correct: 
'http://localhost:4567/suggest?q=' + q
for the saved variable user input and the get request name, and the port, etc. your search bar should work!

With the server running, go to:
'http://localhost:4567/search

As you type words into the search bar, you should see a list of words and a number with the time it took the server to run the search. 

Experiment with ways that your /suggest/ request could be faster or slower.

Here are two ways I saved you from implementing a slow search: 

* I took the opening of the dictionary file out of the /suggest/ call, saving the time of having to load it out of the request time. Now it loads immediately as the server starts. 

* I saved the regular expression as a variable outside the .each iteration. This way the time it takes to build the regular expression is not compounded in memory by every iteration.

These two modifications significantly improved run time to < 1 sec. There are further optimizations but the tests to gauge improvement would need to be more comprehensive, as the searches can vary widely in run time based on the particular combination of letters. Also as more advanced search algorithms are considered, such as organizing the dictionary as a trie, the purpose and functionality of the search bar comes into question: does the search bar actually need to be faster? Does complexity outweigh simple, less error prone design? 

I hope you had fun! I did!

[file]: http://www.math.sjsu.edu/~foster/dictionary.txt

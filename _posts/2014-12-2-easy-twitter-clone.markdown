---
layout: post
title:  "Easy Twitter Clone"
date:   2014-12-2 18:05:42
tags: ["interview", "ruby-on-rails", "front"]
---
Ok so here's the scenario: You come in for an interview for a Ruby on Rails junior dev position. The interviewer wants to
see the ease with which you can understand a feature and implement it quickly in Rails. However, she or he, doesn't have the time to explain an existing app, with it's specific gemset, features, and tests. Therefore, if you are asked to build an app, it's likely to be a simple one, and the challenge here is twofold, to comeplete the critera and create a working app

Don't groan too hard when you hear the task:
build a Twitter Clone,
no user authentication required.

There are so many ways someone can get bogged down in this project, and if you had three hours, you could go down into that bog, add additional features to the models, overdo the testing, throw in bootstrap or some css to make it look pretty.
Becuase you have to demonstrate what you know, right? WRONG. The objective is to demonstrate that you can get an app working based on the specifications. You might only have 30min or less. Any ambiguity or hang up will leave you with a bunch of excuses instead of a functional app (a very basic one, I might add).

First,

* Scope out the project based on the requirements. You want the bare minimum implementation that completes the criteria.

The twitter clone must have a place to enter new tweets and display existing tweets. Without user authentication, this is a one page app, a table with one model and one field you need to create (in addition to the ones Rails automatically creates).

Second,

* Divide your time. 1/2 to 3/4 should be spent creating the MVP, the rest should be spent making sure it actually works, acconting for basic edge cases and bugs.

If you are afraid you won't get to show off your TDD skills or all the nuanced techniques you use on your more complex applications remember you can always articulate to your interviewer what you would do if you had more time.

Ok Twitter Clone, go!


`rails new twitter+`

`cd twitter+`

`rails g scaffold tweet body:text`

`rake db:migrate`

`rails s`

(Here I already encountered a ruby -v, gemset issue that took me 3+ min to sort through)

Great with Rails scaffold we already have page that has a title and a link for a new tweet.
We can create the new tweet in the new page, and we are redirected to the show page for that tweet.
Then we have to click back to see all the tweets.
Let's get it all down to one page.

Don't forget to create a git repo, to demonstrate best practices.

`git init`

`git add .`

`git commit -m "Initial Commmit`


First lets add the create new tweet form to the tweets_index page.

Adding the instance variable to the model for the new tweet.
{% highlight ruby %}
def index
    @tweets = Tweet.all
    @tweet = Tweet.new
  end
{% endhighlight %}

On the index.html.erb, render the new tweet form partial.
{% highlight ruby %}
<%= render "form" %>
{% endhighlight %}
And let's redirect the creation of the tweet to the tweets main page:

{% highlight ruby %}
def create
respond_to do |format|
      if @tweet.save
        format.html { redirect_to tweets_path, notice: 'Tweet was successfully created.' }
        format.json { render :show, status: :created, location: @tweet }
end
{% endhighlight %}

We can take out the show, edit, and destroy links on the index page, and the new tweet link at the bottom. Instead we can make the show page link the time since the post was created:
{% highlight ruby %}
<% @tweets.each do |tweet| %>
      <tr>
        <td><%= tweet.body %></td>
        <td><%= link_to "#{ distance_of_time_in_words(tweet.created_at, Time.now) } ago", tweet %></td>
      </tr>
    <% end %>
{% endhighlight %}

OK, we have a working app, let's start testing.

* What if someone enters blank tweets? What if someone writes more that 140 characters?
Let's add validation to the model:

{% highlight ruby %}
class Tweet < ActiveRecord::Base
  validates :body, presence: true, length: { maximum: 141 }
end
{% endhighlight %}
Try these erroneous inputs to see if they are handled properly.

* What if the request to post a tweet isn't handled fast enough, and without a visual indication, the user repeatedly clicks, consequently reposting the same tweet multiple times?

We can handle this case on the server side and the client side.

On the client side, let's use javascript to make the button disabled once the tweet is submitted.
Go into the tweets.js (take the coffee type off the end) file under app/assets/javascripts

{% highlight ruby %}
$(function() {
   $('#new_tweet').submit(function(ev) {
    $('input[type=submit]').prop('disabled', true);
  })
});
{% endhighlight %}
The function won't be called upon page load, but will wait for the #new_tweet form to be submitted. Then the function will be called to disable the button ('input[type=submit]') with the prop method. The button will fade slightly.


But javascript might not work, so let's add controller checks.
Here we can add two protections:

* If the content of the tweet is the same as the last, prevent posting.
* If < 1 or 2 seconds has not past from the last tweet post time stamp, prevent posting.

To get this working we can try out a couple things in the create controller method.
First add, `sleep 1` or some time for the number to simulate a slow loading browser.
We need to find the latest tweet with the .order method.
Then find the time difference from most recent tweet post to now.

{% highlight ruby %}
last_tweet = Tweet.order('id DESC').first
delta = Time.now - (last_tweet.try(:created_at) || (Time.now - 100))
{% endhighlight %}
Here a case is added for delta if there are no tweets in the database yet.
You can test out your variables in the Rails console.

We can make sure our variable are set correctly with some logger statements below the variables:
{% highlight ruby %}
Rails.logger.info "Delta: #{ delta }s"
Rails.logger.info "strings match: #{ @tweet.body == last_tweet.body }"
{% endhighlight %}

Now we can add the conditional check.
{% highlight ruby %}
if @tweet.body == last_tweet.body && delta < 2
  Rails.logger.info "Preventing tweet"
  flash[:error] = "You just Tweeted that."
  return redirect_to :back
end
{% endhighlight %}
Here you can set the delta time comparison to however long you want the pause, of course. Then disable the javascript (add some misplaced characters) and try to resubmitt tweets repeatedly. (re-enable the javascript when you finish)

Do we need more tests? TDD, test everything, etc.
Here, we don't have time, and I'd argue no. We haven't written longer custom logic than about five lines. We've tested standard usage and the most common cases that could lead to errors. The app is painfully simple but quite robust, and we've completed it within the time limit.

Most importantly it works.


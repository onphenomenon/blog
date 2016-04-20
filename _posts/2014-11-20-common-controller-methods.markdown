---
layout: post
title:  "Common Controller Methods"
date:   2014-11-20 18:05:42
tags: ["ruby-on-rails", "front"]
---
I'm DRY 'ing' up my Reddit clone code as I go along, and since the first time around, any new pattern or practice can prove challenging, I'm documenting simple practices you can throw into your frequently used Rails tool box.

I have four controllers with similar methods for 'save' and 'destroy', in the Topics, Posts, Favorites, and Comments Controllers.

Here's an example for the comments_controller:
{% highlight ruby %}
def destroy
    @comment = Comment.find(params[:id])
    @comment.status = :deleted
    if @comment.save
      flash[:notice] = 'Comment Deleted'
      redirect_to @comment.post
    else
      flash[:error] = 'Comment not deleted'
      redirect_to @comment.post
    end
  end
{% endhighlight %}

Now in the application_controller let's make a generic method that can be used for instances of any of these four controllers.
{% highlight ruby %}
def my_destroy(object, redirect)
    object.status = :deleted
    if object.save
      flash[:notice] = "#{object.class} deleted"
      redirect_to redirect
    else
      flash[:error] = '#{object.class} delete failed'
      redirect_to redirect
    end
  end
{% endhighlight %}

Note how two arguments are necessary because the redirect is particular to the desired behavior of the particular object. Also see how the name of the class of the object can be interpolated into the string message with the `.class` method.

The destroy method in the comments controller is significantly reduced:
{% highlight ruby %}
 def destroy
    @comment = Comment.find(params[:id])
    my_destroy(@comment, topic_post_path(@comment.topic, @comment.post))
 end
{% endhighlight %}

Similarly, here is the topics_controller create method before
{% highlight ruby %}
def create
    @topic = Topic.new(topic_params)
    if @topic.save
      flash[:notice] = 'Topic saved'
      redirect_to @topic
    else
      flash[:error] = 'Topic not created'
      render :new
    end
  end
{% endhighlight %}

The generic "save" method in application_controller:
{% highlight ruby %}
def my_save(object, redirect)
  if object.save
    flash[:notice] = "#{object.class} saved"
    redirect_to redirect
  else
    flash[:error] = "#{object.class} not saved"
    redirect_to redirect
  end
end
{% endhighlight %}

And the topic_controller create method after:
{% highlight ruby %}
 def create
    @topic = Topic.new(topic_params)
    my_save(@topic, topics_path)
  end
{% endhighlight %}


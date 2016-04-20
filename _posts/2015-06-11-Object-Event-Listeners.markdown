---
layout: post
title:  "Object-Event-Listeners"
date:   2015-06-11 18:05:42
tags: ["front"]
---

MVC frameworks like Backbone and Angular take care of special object functionality that makes objects (Models, Views, Controllers) able to respond to events that occur in other objects.

While these methods (ex: `.on("event")`) may seem magical, the event system interface is not that obtuse.



For example, we have an aquarium object model, and as aquarium view object, and an aquarium controller object.

The user clicks on 'add fish' in the view, we need this event to be relayed to the controller, then the model, then back to the view to add the fish to the scene.

Some pseudo code could be:

{% highlight js%}
var aquariumView = {};
var aquariumController = {};
var aquariumModel = {};
aquariumView.addFish = function() {
  aquariumController.talkToModel();
}
aquariumController.talkToModel = function(){
  aquariumModel.insertFishdb();
}
{% endhighlight %}

But these are specific relationships to keep track of for a particular MVC. Let's create our own event system mixin to add to any object to generalize this pattern.

First we create a method on the object that takes an event as an argument and decides what to do with it. A names that make sense for this method is `trigger`.


{% highlight js%}
  myobject.trigger = function(event) {
    // what the object should do when the event occurs.
    // needs to have a store somewhere of what other objects
    // want it to do when the event occurs.
  }
{% endhighlight %}

Create an interface to register what other objects should do when the event occurs on myObject.

{% highlight js%}
  myobject.on = function(event, callback);

  //We want to be able to register what myView should do when the newFish event occurs on myObject like this, myobject.on('newFish', myView.addFish() );

  // we need a place to store this association:
  myObject.reactionsTo = {}
  myobject.on = function(event, callback) {
    this.reactionsTo[event] = this.reactionsTo[event] || [];
    this.reactionsTo[event].push(callback)
  }
  //Use this event callback storage to tell `.trigger` how myObject should handle an event.

  myobject.trigger = function(event) {
    this.reactionsTo[event].forEach(function(callback){
        callback();
    })
  };

  //Now I can register the event callback:

  myobject.on('newFish', myView.addFish() )

{% endhighlight %}

When `myObject.trigger('newFish')` occurs, myView will respond with the method to add the fish to the view.

These methods `.on` and `.trigger` can be abstracted to an interface we can add to any object.

{% highlight js%}

var addEventHandler = function(object) {
  var reactionsTo = {};

  object.on = function (event, callback) {
    events[event] = events[event] || [];
    events[event].push(callback);
  };

  //add additional functionality for arguments
  object.trigger = function(event) {
    if(reactionsTo[event]) {
      var args = Array.prototype.slice.call(arguments, 1);
      reactionsTo[event].forEach(function(callback) {
        callback.apply(object, args);
      })
    }
  }

  return object;
}

{% endhighlight %}

Now I could easily add the event handler mixin to any object as the basis for a custom framework or just understand how frameworks manage events.

{% highlight js%}
addEventHandler(aquariumView);
addEventHandler(aquariumController);

aquariumView.on("addFish", aquariumController.talkToModel(newFishy));
aquariumController.on("talkToModel", aquariumModel.insertFishdb(newFishy));

//Someone clicks on the `Add blowFish` button:
aquariumView.trigger("addFish");

{% endhighlight %}

Our controller and model responds accordingly to the event!



---
layout: post
title:  "Object Oriented JavaScipt"
date:   2014-12-13 18:05:42
tags: ["front", "interview"]
---
JavaScript heavy applications can benefit from using objected oriented features of JavaScript to develop code modularity. Having programmed in Ruby, an object oriented programming language, JavaScript's version of OOP is somewhat different.

JavaScript's OOP style is prototype-based progamming, where classes are not present, and the inheritance mechanism for behavior reuse is achieved through adding additional features to existing objects, which act as prototypes. The phrase instance-based programming also fits JavaScripts version of OOP well .

Here is an example of creating a JavaScript Video prototype/object with the function watch(). The properties can be entered as single argument variables or as a hash of options, so order does not matter. The options to the right of the double lines in the prototype constructor are the default properties. I reuse the prototype to create a instance of the object and use can use the prototype function on the new instance.


{% highlight js%}
function Video(config){
    config = config || {};
    this.title = config.title || "Untitled"
    this.user = config.user || "Unknown"
    this.seconds = config.seconds || 0;
}

Video.prototype.watch = function() {
    console.log("This video is " + this.seconds+ " seconds long.");
};

var newVid = new Video({title: "Dumbo", user: "Kari", seconds: 60});
newVid.watch();

{% endhighlight %}
OUTPUT
`This video is 60 seconds long.`


Now I will create a JavaScript object MusicVideo that inherits from the Video prototype. The constructor looks similar to the original constuctor of Video except it includes a callback to the Video, with Video's original contstructor arguments, along with 'this', which references the scope of the MusicVideo object and it's unique properties.

Once the constructor is created I link the MusicVideo prototype with the new Video() constructor, and the Music video constructor with the MusicVideo constructor function I created.

Then I can add additional methods to the MusicVideo prototpe and instantiate a new MusicVideo object.

{% highlight js%}
function MusicVideo(config) {
    Video.call(this, config);
    this.genre = config.genre;
}

MusicVideo.prototype = new Video();
MusicVideo.constructor = MusicVideo;

// A new method on this object
MusicVideo.prototype.play = function() {
  console.log("Wow this " + this.genre + " music, sucks!");
};

// Instantiating a new object
var musicVid = new MusicVideo({title: "La Bamba", userr: "Kari", seconds: 250, genre: "Polka"});
musicVid.play();
musicVid.watch();

{% endhighlight %}

OUTPUT:

`Wow this Polka music, sucks!`
`This video is 250 seconds long.`


These basic pattern of OOP in JavaScript can be applied to a MVC framework as shown in my next post!

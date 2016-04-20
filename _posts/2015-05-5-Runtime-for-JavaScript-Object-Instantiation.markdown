---
layout: post
title:  "JavaScript Object Instantiation Patterns and Runtime"
date:   2015-5-16 18:05:42
tags: ["front"]
---

JavaScript uses a style of objected-oriented programming called prototype-based programming, unlike for instance, Java which uses a class-based object-oriented style. Because of this, JavaScipt adheres to a couple different patterns of object instantiation.

To instantiate an object based on a pattern, all that is required is a function that returns an object with properties and methods. Java on the other hand requires a class for every object, with inheritance clearly stated, defining object specifications, accessor methods, and a constructor method.

There are four key instantiation patterns in prototype-based programming for JavaScript which I will demonstrate through the creation of stack and queue objects. Then I will test the runtime of each instantiation pattern, as no pattern is off-limits, but choosen based on the complexity, speed, and storage requirements of an application.

1) Functional Instantiation:
This object creating function includes all the properties and methods of the object. Drawbacks include dublicate method creation for each new instance of the object.

{% highlight js%}
//Queue: First in First Out ordered data structure
var functionalQueue = function() {
  var storage = {};
  var start = -1;
  var end = -1
}

functionalQueue.enqueue = function(value) {
  end++;
  storage[end] = value;
}

functionalQueue.dequeue = function() {
  functionalQueue.size() && start++;
  var temp = storage[start];
  delete storage[start];
  return temp;
}

functionalQueue.size = function() {
  return end-start;
}

{% endhighlight %}


2) Functional Instantiation with Shared Methods:
A separate object holds all the methods that will be shared by instances of the object. Because the methods live somewhere other than the constructor function but are shared with the instantiated object through extend, the instantiated object has references that point to the methods object, but the methods do not need to be re-created for each new object instantiation.

{% highlight js%}
//Stack: Last in First Out ordered data-structure

var functionalSharedStack = function() {
  var someStack = {};
  someInstance._storage = {};
  someInstance._size = 0;
  extend(someStack, stackMethods);
  return someStack;
}

var stackMethods = {}

stackMethods.push : function(value) {
    this._storage[this._size] = value;
    this._size++;
};
stackMethods.pop : function() {
    this._size && this._size--;
    var temp = this.storage[this.size];
    delete this._storage[this.size];
    return temp;
};
stackMethods.size : function() {
    return this._size;
};

var extend = function(obj1, obj2) {
  for(var key in obj2){
    obj1[key] = obj2[key];
  }
}

{% endhighlight %}

3) Prototypal Instantiation:
All JavaScript objects have a link to a prototype object, and we create internal links between objects through the prototype chain. `Object.create(prototype object)` creates a new object with the specified prototype object and properties. Key to remember is that the keyword `.prototype` on method definitions does not automatically create internal linkages; thus, I will use `.methods` as the container for the parent methods.

{% highlight js%}
//another Queue implementation
var prototypalQueue = function() {
  var queueInstance = Object.create(prototypalQueue.methods);
  queueInstance._storage = {};
  queueInstance._start = -1;
  queueInstance._end = -1;
  return queueInstance;
}

prototypalQueue.methods = {};

prototypalQueue.methods.enqueue = function(value) {
  this._end++;
  this._storage[this._end] = value;

}
prototypalQueue.methods.dequeue = function() {
  this.size() && this._start++;
  var temp = this._storage[this._start];
  delete this._storage[this._start];
  return temp;

}
prototypalQueue.methods.size = function() {
  return this._end-this._start;
}

{% endhighlight %}

4) PseudoClassical Instantiation:
PseudoClassical stlye of object creation uses the keyword new upon instantiation and both implicitly calls `Object.create()` and returns the object in the constructor. It is the tighest code, but understanding of what is going on under the hood is necessary for full comprehension of the "magic" object instantiation with return object statement.


{% highlight js%}
//implementation; var myStack = new Stack()

var Stack = function() {
  this._storage = {};
  this._size = 0;
}

Stack.prototype.pop = function() {
  this.size() && this._size--;
  var temp = this._storage[this._size];
  delete this._storage[this._size];
  return temp;

};
Stack.prototype.push = function(value) {
  this._storage[this._size] = value;
  this._size++

};
Stack.prototype.size = function() {
  return this._size;
};



{% endhighlight %}

In order to demonstrate the runtime of object instantiation for these four patterns I created an html page that creates 10,000 stack and queue objects per type.

Here's the repo:
[instantiationTest](https://github.com/onphenomenon/instantiationTest).

The runtime comparisions are shown here in milliseconds for 10,000 stack and queue objects.

* Function: 27.268999998341314
* Functional-Shared: 32.06800000043586
* Prototypal: 3.966000003856607
* Pseudo: 2.510999998776242

I used the console log with performance.now() in the script, but another way to make runtime comparisons is with the CPU profiler in the chrome console.

Here the runtime differences are clear in the instiation styles. PseudoClassical style wins by a longshot, though prototypal is not far behind. Plain function objects with all their properties and methods contained is the slowest, and runtime increases rapidly as object number increases.

Other runtime comparsions that could be done are .push() .pop() .enqueue() .dequeue() operations based on instantiation patterns.

PseudoClassical style instantiation has been optimized by the JS community of developers, making it the fastest choice for object instantiation, though not always necessary, as it increases code complexity and plain unadulterated JS works just fine in many cases.



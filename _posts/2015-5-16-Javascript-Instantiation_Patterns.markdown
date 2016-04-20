---
layout: post
title:  "JavaScipt Primitives vs. Object"
date:   2015-5-14 18:05:42
tags: ["front"]
---

**Misconception**: "Everything in Javascript is an object".

**Reality**: There are six types in JS:

* Simple Primitives:
  * string
  * boolean
  * number
  * null
  * undefined
* object

There is a bug in the language that causes `typeof null` to return "object" incorrectly.
However, null is it's own primitive.

Functions and arrays are sub-types of object.

There are other built-in object sub-types, String, Number, Boolean, Date, Error, which appear to be acutal types or tied to the primitive counter-parts with the same name. However, these capitalized primitives are constructor functions for new objects of that specific sub-type. However, JavaScript automatically coerces a primitive string, number, or boolean literal to a String, Number, or Boolean object when necessary (you try to use object methods on it). Thus, magically you can use the methods on these object sub-types, such as toString(). (primitives do not strictly have access to these methods.) The standard among the JS community is not to use the constructed object form, thus relying on the internal type coersion of literals.

{% highlight js%}
//preferred literal construction:
var strPrimitive = "Hey I'm a string"
strPrimitive instanceof String;
//false
myString.toUpperCase();
//automatic object conversion: "HEY I'M A STRING"
//what's actually happening but not preferred:
var myString = new String("Hey I'm a string");
myString.toUpperCase();
{% endhighlight %}

JavaScript is perhaps the best known prototype-based programming language, which employs cloning from prototypes rather than inheriting from a class (contrast to class-based programming).

Prototype-based programming is a style of object-oriented programming in which behaviour reuse (known as inheritance) is performed via a process of cloning existing objects that serve as prototypes.

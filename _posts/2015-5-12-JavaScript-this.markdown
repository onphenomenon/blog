---
layout: post
title:  "this JavaScipt"
date:   2015-5-12 18:05:42
tags: ["front"]
---
  This post will explore misconceptions and the four main rules of how to identify the runtime binding of "this" .

  Our aim in using the identifier "this" is to implicitly "pass along" a reference object.

  The main misconceptions include

  1. "this" refers to the function itself.
  * To reference a function object from inside itself use a reference to to function object with the lexical identifier. Cardinal sin: Don't use "this" inside a function object to reference it.

  2. "this" refers to the function's lexial scope.
  * The reference object for "this" is bound at runtime, not where the function is declared. To determine runtime binding, we must determine the call-site, which is based on execution context and the call-stack. If we look at the call-stack, the call-site is the second item form the top.

  Now, let's examine the four main rules of "this" binding based on call-site. Of course, there are other more complex considerations, but understanding the main constraints is imperative.

  1) Default Binding :
  "this" in a stand alone function invocation, refers to the global object. If "use strict" is defined in the function, the global object is not available for default binding, "this" will return "undefined". The call-site is the global scope.

{% highlight js%}
  function foo() {
    console.log(this.a)
  }
  var a = 2;
  foo();
  //Output: 2

  function tree() {
    "use strict";
    console.log(this.a);
  }
  var a = 2;
  foo();
  //TypeError: "this" is "undefined".
{% endhighlight %}

  2) Implicit Binding :
  If call-site has a containing object: obj.foo(), and the object contains a function reference (foo), "this" is bound to the object.

{% highlight js%}

  function foo() {
    console.log( this.a );
  }
  var obj = {
    a: 2,
    foo: foo
  }
  obj.foo();

{% endhighlight %}

  3) Explicit Binding
    If we want to force a function call to use a particular object for "this" binding without putting a property function reference on the object. All functions have the call(...) and apply(...) methods which take as their first parameter and object to use for the "this", then invoke the function with that "this specified".

{% highlight js%}
  function foo() {
    console.log( this.a );
  }
  var obj = {
    a: 2
  };
  foo.call(obj);
  // 2


{% endhighlight %}
  HARD BINDING:
  However there is still a danger of a function "loosing" it's intended "this" binding or overridden by a native function (setTimeout) or framework even with explicit binding. We can solve this by wrapping a .call or .apply in a different function, to manually force that function to bind "this" with our object.

{% highlight js%}
  function foo() {
    console.log( this.a );
  }
  var obj = {
    a: 2
  };
  var bar = function() {
    foo.call( obj );
  };

  bar();
  //2
  setTimeout( bar, 100 );
  //2
  bar.call( window );
  //2, hard-bound bar cannot be overridden.

{% endhighlight %}

  4) New Binding
    JS constructor functions can be called with the "new" operator.
    When a function is invoked with "new", a constructor call,
      a new object is created and returned.
      the new object is prototype linked,
      the newly created object is set as "this" binding for the function call.

{% highlight js%}
  function foo(a) {
    this.a = a;
  }

  var bar = new foo( 2 );
  console.log( bar.a );
  //2
{% endhighlight %}

  Great, so with the four rules outlined, all we need to do is find the call-site and see which rule applies. However, what if the call-site has multiple eligible rules?

  Precedence of "this" binding rules, take the first rule that applies for "this" binding:

  1. "new" function call.

  2. explicit binding (.call, .apply) function call, with hard binding overridding.

  3. implicit binding, function called with context of owning object.

  4. default binding, a free function call with "strict mode" having not access to the default global object.

  For more exmaples of how to untangle complex "this" situations, see the reference article.

 This post is based on a summary of "You Don't Know JS", chapter two:
  [this: all makes sense now!](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20&%20object%20prototypes/ch2.md).

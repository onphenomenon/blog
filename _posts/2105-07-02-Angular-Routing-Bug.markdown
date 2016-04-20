---
layout: post
title:  "Angular Redirect Hiccups"
date:   2015-07-02 18:05:42
tags: ["front"]
---

While working on angular redirects in project using Ionic, I came across a perplexing glitch, a redirect that would only work on the second click.

Upon sucessful signin using a promise callback, I routed to the main page using the $location service and ui-router.

{% highlight js%}
//from auth.js, authController
.controller("AuthCtrl", function (FetchEvents, $timeout, $scope, $rootScope, $location, $window, Auth){

  $scope.signin = function () {

    Auth.signin()
      .then(function (authResult) {

        $rootScope.currentUser = authResult;

        ///HERE IS THE ERROR, WILL SHOW fix below.
        $location.path("/app/main");

      })

  };

{% endhighlight %}

Every variable was successful for a url path change on the first click, even logging out $location.path() showed that the url had changed to "/app/main", but the view hadn't responded.

The answer is that $location.path() was being run too soon. The $rootScope event "$stateChangeStart" was only being registered on the second click. The angular page processes didn't have time to run before the path was changed.

The key is to put the path change into a timeout function:

{% highlight js%}
$scope.signin = function () {

    Auth.signin()
      .then(function (authResult) {

        $rootScope.currentUser = authResult;

        $timeout(function () {
          $location.path("/app/main");
          }, 0);

      })
  };

{% endhighlight %}

Even with no delay, the $timeout creates the pause needed for the angular processes to catch up and successfully register both the url path change and the "$stateChangeStart" event on the first click.

I am still not quite satisfied with this solution, however it works, based on what angular.js is taking care of in the background, giving it time to respond. Unlike backbone.js, angular.js takes care of lots of easy functionality at the price of sometimes not being able to debug or modify the compenents that create the interface or fully understand all the processes that need to be completed for the components to function as expected.

For another example of this work around angular routing glitch, here's a fiddle that demonstrates this patch further:
[Angular Routing Fiddle](http://jsfiddle.net/mcpDESIGNS/7Ah2W/1/);



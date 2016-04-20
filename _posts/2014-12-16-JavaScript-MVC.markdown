---
layout: post
title:  "JavaScript MVC"
date:   2014-12-14 18:05:42
tags: ["front"]
---
In decades not so long ago, the internet ran on HTML to receive and send data requests from a server . This is a reason why page re-loading was so slow.

A step of progress is to request a much smaller JSON data object, that holds the attributes in a format that mimics Javascript's object literal syntax. Javascript or another programming language can be used to parse the JSON objects and to render the data object's properties within the html. As JavaScript has proved so valuable for client interaction in the browser, today we have  other valuable tools built on JavaScript to make the process of fetching data event faster (AJAX) and added to the html seamlessly without page reloading (jQuery).

Since Javascript code will start to pile up quickly, a process of organization is paramount to keeping code understandable and functionable. With Object Oriented Javascript, the simplest delineation to make is separating the "Model"--data fetch and process--from the "View"--creating DOM and handling DOM events.

In my example, my model will handle individual youtube videos, whose data is stored an array of JSON video objects. The JSON array will be loaded statically, but in a subsequent post, I will show how AJAX can be used to load the JSON data from the server.


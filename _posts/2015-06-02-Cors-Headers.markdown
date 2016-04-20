---
layout: post
title:  "Cors Headers"
date:   2015-06-02 18:05:42
tags: ["front"]
---

HTTP access control or CORS (Cross-Origin Resource Sharing) can be a sticking point for developers attempting to access an API or to create a server.

When a client makes an HTTP request for a resource in a different doimain, the client's request or "pre-flight" first checks to see whether their request is acceptable to the server with an HTTP OPTIONS request method. The server responds to this initial connection with HTTP headers that describe what requests are permitted and by whom. If the server approves the type of request the browser solicits, the browser sends the actual request with the actual HTTP request method.

I created a simple chat client app with pure node in order to learn the basic functionality of creating a server and processing request with CORS. I began this app with a pure javascript/jquery implementation which I then converted to a backbone framework. In doing so I became familiar with both the $.ajax, 'get' and 'post' as well as the backbone fetch() and send() methods, and how they interact with server-side CORS.

In order to use node, you need npm. A basic understanding of module.exports an exports in node is helpful for understanding node's module system.

Two modules that were extremely helpful in pure node development were node-inspector (to place debug statements in server side code) and nodemon (to automatically restart the server when changes occured), both used the -g flag to install globally. I used bower to manage dependencies client-side, which included the jquery, backbone, and underscore libraries.

My server used the http module, hosted on a local port:

{% highlight js%}
//basic_server.js
var http = require("http");
var handleRequest = require('./request-handler');

var port = 3000;
var ip = "127.0.0.1";
console.log("Listening on http://" + ip + ":" + port);

var server = http.createServer(handleRequest);
server.listen(port, ip);

{% endhighlight %}

My request-handler module dealt with get and post requests. `module.exports = requestHandler;` was important for exporting the module to my main server file. In request-handler, and important variable was my defaultCorsHeaders. Also a sendResponse function wrapped repeated code for assembling the response object.

{% highlight js%}

var defaultCorsHeaders = {
  "access-control-allow-origin": "*",
  "access-control-allow-methods": "GET, POST, PUT, DELETE, OPTIONS",
  "access-control-allow-headers": "content-type, accept",
  "access-control-max-age": 10 // Seconds.
};

exports.sendResponse = function(response, data, statusCode){
  statusCode = statusCode || 200;
  response.writeHead(statusCode, headers);
  response.end(JSON.stringify(data));
};

{% endhighlight %}

These Cors headers are essential in the pre-flight stage of the browser request `request.method = "OPTIONS"`.

In the (request, response) body for handling pre-flight CORS protocol, the OPTIONS request response with headers allows the browser to check the `access-control-allow-methods` to see if the request solicited by the browser is acceptable to the server.

Here are the actions object that map RESTful actions to the the response to send.

{% highlight js%}

var actions = {
  'GET': function(request, response){
    utils.sendResponse(response, {results: messages});
  },
  'POST': function(request, response){
    utils.collectData(request, function(message){
      message.objectId = ++objectId;
      messages.push(message);
      utils.sendResponse(response, {objectId: objectId}, 201);
    });
  },
  'OPTIONS': function(request, response){
    utils.sendResponse(response);
  }
};

exports.requestHandler = function(request, response) {
  var action = actions[request.method];
  if( action ){
    action(request, response);
  } else {
    utils.sendResponse(response, "Not Found", 404);
  }
};


{% endhighlight %}

Depending on the method used by browser to make the request (I will give both ajax and backbone examples), if this intial `OPTIONS` CORS request is successful, the browser will immediately send the actual request.

The GET request includes the data array stored on the server file that must be put into JSON format with `JSON.stringify(data)`.

The POST request uses the read method to process the message, build a chat string, and is JSON.parsed before put into the data array serer-side.

This is very basic CORS protocol for GET and POST requests that I hope is an intial starting point for comprehension.

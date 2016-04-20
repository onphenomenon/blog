---
layout: post
title:  "Asynchronous Solutions, Nested Callbacks to Promises"
date:   2015-06-06 18:05:42
tags: ["front"]
---

For limited asynchronous callbacks, importing a library to handle sequencing is not always necessary. Understanding the callback process is enough to create logical nesting to achieve timely handling of requests.

For example, in a recent app, my client issues a GET request on interval to recieve all updates to the mySQL database. Because the mySQL query takes time to finish, the get request must be handled asynchronously.

My controller for the `/messsage` route handles get requests, and issues the function on the messages model to get all the messages from the database. The callback in
`models.messages.get` retains access to the messagePackage returned by the query once it is finished, in order to send it in response object.

{% highlight js%}
//controller routes
module.exports = {
  messages: {
    get: function (request, response) {
      //use models to access the message objec to send to the response
      var messagePackage = models.messages.get(function(messagePackage){
        console.log("Here in the get request", messagePackage);
        sendResponse(response, messagePackage);
      });
{% endhighlight %}

The models module, which handles the mySQl query uses the callback in it's argument to do what is requested with the return variable when the query is finished.

{% highlight js%}
//model routes
module.exports = {
  messages: {
    get: function (callback){

      var messagePackage = {};
      messagePackage.results = [];

      connection.query('select user_name, message_text, room_name from messages', function (err, rows, fields){
        if (err) throw err;
        for (var i in rows) {
          var message = {};
          message.username = rows[i].user_name;
          message.text = rows[i].message_text;
          messagePackage.results.push(message);
          }
          //have access to message package, query is finished, send response.
          callback(messagePackage);

      });
    },
{% endhighlight %}

For interest in how this fits into a modular request structure, separating the controller and model logic allows us to keep the code DRY. The router module allows us to integrate this controller and model logic into the various but repeated url paths.


{% highlight js%}
var controllers = require('./controllers');
var router = require('express').Router();

for (var route in controllers) {
  router.route("/" + route)
    .get(controllers[route].get)
    .post(controllers[route].post);
}

module.exports = router;
{% endhighlight %}




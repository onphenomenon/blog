---
layout: post
title:  "JavaScipt Data Structures"
date:   2015-5-15 18:05:42
tags: []
---
Exercise: Implement common data structures in JavaScript using different object pattern styles.

Let the fun begin!

Linked-List, functional-style.

{% highlight js%}
var LinkedList = function(){
  var list = {};
  list.head = null;
  list.tail = null;

  list.addToTail = function(value){
    var newTail = Node(value)
    if(!list.head){
      list.head = newTail
    }
    if(list.tail) {
      list.tail.next = newTail;
    }
    list.tail = newTail
  };
  list.removeHead = function(){
    if(list.head === null){
      return null;
    }
    var currentHead = list.head;
    list.head = list.head.next;
    return head;
  }
  list.contains = function(target){
    var node = list.head;

    while(node){
      if(node.value === target) {
        return true;
      }
      node = node.next;
    }
    return false;
  }
  return list;

}
var Node = function(value){
  var node = {};
  node.value = value;
  node.next = null;
  return node;
}
{% endhighlight %}
//tree object in functional shared-method style.



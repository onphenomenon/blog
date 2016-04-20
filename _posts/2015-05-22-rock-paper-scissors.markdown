---
layout: post
title:  "Recursive Combination Algorithms"
date:   2015-05-28 18:05:42
tags: ["front", "interview"]
---

Question: How many different combinations are there for n items choosen n at a time with replacement?

Example: In a standard rock-paper-scissor game we throw down three moves (yes, only the last throw actually matters but we are more interested in the combintations). Create an algorithm that outputs an array of the posssible combinations for three moves. Expand the solution to output combinations for n moves.

If the number of moves is limited to three, a nested for loop could easily allow us to acculuate each combination. However with every increased move, another nested loop would be required.


{% highlight js%}
var RPS = function() {
    var outcomes = [];
    var moves = ["rock", "paper","scissors"];

    for(var i = 0; i < moves.length; i++){
        for(var j = 0; j < moves.length; j++){
            for(var k = 0; k < moves.length; k++){
                outcomes.push([moves[i], moves[j], moves[k]]);
            }
        }
    }
    return outcomes;
}
RPS();

{% endhighlight %}

What if we wanted to find the possible combinations for n rounds? Only a recursive solution makes sense. The recursive subroutine will build arrays of rounds length n, at each addition passing the partially completed combination back into the recursive function in order to build a combination with each of the three possibilities at that index of the play array. The base case stops the recursive call when rounds left is equal to zero, which is reduced at each call to the function, and the completedPlay of rounds length is added to the outcomes array.

{% highlight js%}
var rockPaperScissors = function(rounds) {
    //array for all outcomes
    var outcomes = [];
    var plays = ["rock", "paper", "scissors"];
    var onePlay = [];

    var buildaPlay = function(roundsTogo, completedPlay) {
        if(roundsTogo === 0) {
            outcomes.push(completedPlay);
            return;
        }
        for(var i = 0; i < plays.length; i++) {
            var currentPlay = plays[i];
            buildaPlay(roundsTogo-1, completedPlay.concat(currentPlay))
        }
    }
    buildaPlay(rounds, []);
    return outcomes;

}

{% endhighlight %}

To me, the key to thinking about recursive problems is visualizing a tree data structure where each node has similar paths (ex. putting the node into the recursive function). All paths must be traveled and there also must be a sign as when to stop traveling down the nodes of the tree.

Here is a similar problem of increased difficulty:

Given a telephone number, string of digits, where each digit represents a set of letters, create a function that returns all possible letter combinations for that digit string.

This is similar to the rounds of rock, paper, scissors, but for this problem the length of the digits is how many iterations we need on each string we are building until we can reach the base case and push the word to the outcomes array. Also each number has different "plays" or possible letters, and so the for loop must iterate through these possibilities, adding each possible letter to that word fragment, and re-call the recusive function with the next string and the digits remaining.

{% highlight js%}
var phoneWords = function(digits) {
    var words = [];
    var digitArray = digits.split("");
    var length = digitArray.length;


    var oneWord = function(digitstoGo, word){
        //base case, when to push word to array
        if(digitstoGo === 0) {
            words.push(word);
            return
        }

        var currentDigit = digitArray[length - digitstoGo]

        var possibleLetters = phoneDigitsToLetters[currentDigit];
        console.log(possibleLetters)
        var arrayPossible = possibleLetters.split("");

        for(var i = 0; i < arrayPossible.length; i++) {
            var currentLetter = arrayPossible[i];
            oneWord(digitstoGo-1, word.concat(currentLetter));
        }
    }

    oneWord(length, "");
    return words;

}
{% endhighlight %}






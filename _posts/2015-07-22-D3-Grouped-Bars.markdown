---
layout: post
title:  "Grouped Bars D3"
date:   2015-07-21 18:05:42
tags: ["front"]
---

Create a stacked or grouped bar chart with D3 for a data set with multiple parameters per segment.

I wanted to have a visual model for the profit-loss balance sheet for a quickbooks business. Every month includes metrics for total income, cost of goods, total expenses, and net profit.

Stacking the balance sheet data didn't yield visual information; however, by changing the grouped model to display negative values, the grouped pattern ultimately added value to the data presentation.

Month balance sheet objects came from Intuit Quickbooks API. https://developer.intuit.com/

Here I'll comment the code so you can implement a similar visualization for your data, as well as understand what transformations are happening so you can modify your visualization to suit your data.

One piece is creating the svg element according to my specifications.


{% highlight js%}
 var margin = {top: 40, right: 10, bottom: 20, left: 30},
    width = 960 - margin.left - margin.right,
    height = 500 - margin.top - margin.bottom;

var svg = d3.select("body").append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
    .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
{% endhighlight %}

D3.js uses an svg container element to render shapes, however svgs have different styling principles than regular DOM elements that use CSS properties.

We append "g" elements to the svg so we can easily target them when styling. We can use svg transformations to move the "g" element where we want in the svg, for example a scale at the bottom of the page. The first g element sets the inner dimensions for the area we put the graph in, positioning it x coordinates to the left with margin.left, and y coordinates down with margin.top.

Next, I'm going to set the scales for my chart. The y0 scale is useful for figuring out y positions for negative values.

{% highlight js%}

//the xScale will map a domain input to a specified range, so each month (m) will be given mapped to equal space within the width of the svg
var xScale = d3.scale.ordinal().domain(d3.range(m)).rangeRoundBands([0, width], .08);
//the xordinal is similar to the xScale but I wanted to use the months for the xAxis demarcations.
var xordinal = d3.scale.ordinal().domain(months).rangeRoundBands([0, width], .08);
//the xAxis is where I use xordinal to specify x month values along the x axis
var xAxis = d3.svg.axis()
    .scale(xordinal)
    .tickSize(0)
    .tickPadding(6)
    .orient("bottom");

//yScale takes yGroupExtents as a domain, the lowest and the highest values from my dataset
//mapped to the height boundaries
var yScale = d3.scale.linear()
    .domain(yGroupExtents)
    .range([height, 0]);
//for data less than zero I'll use this scale to set the y coordinate to zero within my yScale range.
  var y0 = function() {
    return yScale(0)
  }
//yAxis uses yScale but it is oriented to the left
  var yAxis = d3.svg.axis().scale(yScale).tickSize(1)
    .tickPadding(0).orient("left");

{% endhighlight %}

Now we can start adding the axes to the svg:

{% highlight js%}
//remember we translate the g element down by y 'height' to put it at the bottom (minus the margins)
svg.append("g")
    .attr("class", "axis x")
    .attr("transform", "translate(0," + height + ")")
    .call(xAxis);
//the mid-line or zero axis position is created with the y0() scale.
svg.append("g")
    .attr("class", "axis x zero")
    .attr("transform", "translate(0," + y0() + ")")
    .call(xAxis);
//finally add the y axis along the left side.
svg.append("g")
    .attr("class", "axis y")
    .call(yAxis);
{% endhighlight %}

The last step is adding the rectangle bar elements using the D3 methods for data that updates dynamically.
My "layers" data is my cleaned and processed data with three arrays.
Array one is Cost of Goods.
Array two is Expenses.
Array three is Total Income.

{% highlight js%}

var layer = svg.selectAll(".layer")
    //there are three layers so there will be three elements upon enter
    .data(layers)
    .enter().append("g")
    .attr("class", "layer")
    .style("fill", function(d, i) { return color(i); });
//here each layer gets the rectangle element, with the x and y set for the grouped orientation.
//n is the number of layers
//y checks it the value is less than zero, for a negative nubmer and so uses the y0 scale.
//the height uses Math.abs for negative values
var rect = layer.selectAll("rect")
    .data(function(d) { return d; })
    .enter().append("rect")
    .attr("x", function(d, i, j) { return xScale(d.x) + xScale.rangeBand() / n * j; })
    .attr("y", function(d) { return d.y < 0 ? y0() : yScale(d.y); })
    .attr("width", xScale.rangeBand() / n)
    .attr("height", function(d) { return Math.abs( yScale(d.y) - y0() ); });

{% endhighlight %}

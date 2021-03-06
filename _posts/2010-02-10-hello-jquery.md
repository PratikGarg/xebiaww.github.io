---
layout: post
header-img: img/default-blog-pic.jpg
author: rjaiswal
description: 
post_id: 3114
created: 2010/02/10 09:34:41
created_gmt: 2010/02/10 04:34:41
comment_status: open
---

# Hello jQuery!

JavaScript libraries/frameworks have gained a lot of popularity lately. A small though significant reason for the development / adoption of these libraries has been the large number of browsers, even writing a small piece of JavaScript which works on IE6, IE7, IE8, Firefox 2, 3+ and Chrome can be pain, on the other hand inclusion of a library that can work across browsers can ease the development immensely.

A simple Google search will give you many alternatives like prototype, dojo, yui, extjs and jQuery. Though each library is really good at something (like Scriptaculous for animation, extjs for rich UI), I found jQuery to be generally most useful and functionally very rich. In this blog I will discuss the basic usage of jQuery, its syntax and applications, hope you find it useful.

**jQuery Setup**

jQuery can be downloaded from -

<http://code.jquery.com/jquery-1.4.1.min.js>

If you are working on an internet application, you will be better off downloading it from Google [ http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js][1]

Inclusion of jQuery is adding a standard line in html -

<script language="javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js" />

That is all it takes to setup jQuery.

**Working with jQuery**

Almost all jQuery operations can be divided into two parts - Selection and Manipulation.

jQuery allows you to select an HTML element and carry out operations on it. An example of this would be -

$("#an-id").addClass("error")

Here we have used jQuery to select an HTML element of id "an-id" and add a style class to it. The part before the dot (.) is equivalent to document.getElementById("an-id") but is much easier to write.

Most of the times you would be carrying out jQuery operations / functions on execution of an event, such as clicking a button etc. The question then arises, when do I register my event handlers. You may do something like <input type="button" onClick=".."> but this is generally not considered good practice, with jQuery who can register your handlers in this way -

$(document).ready(function(){    //Register Handlers    $("#mainLink").click(function(event){      alert("Thanks for visiting!");   }); });

The inclusion of the above lines anywhere in your HTML code is good enough to register your handlers with jQuery. The advantage here is that the code is executed after the DOM has been loaded but before the images have been downloaded.$(...).

**Selecting elements with jQuery**

Selector
Example
Explanation

#id
$("#id")
Selection through a DOM id

element
$("li")
Returns all <li> elements</li>

.class
$(".error")
Returns all elements with <.. class="error" ...

element.class
$("div.error")
Returns all elements with <div class="error" ...

elem1 elem2
$("div.a div.b")
Returns all elements <div class="b"> which are inside

elem[att=val]
$("a[href=page1]")
Returns all elements with <a href="page1">

elem[att^=val]
$("a[href^=page]")
Returns all anchor elements with attribute href's value starting with page (Regex) 

Though there are many other types of selectors I have covered the basics here and leave it upto you to explore as per your needs.

**Some operations with jQuery**

$(...).html("<b>Hello</b>")
Sets the inner HTML to **Hello**

$(...).addClass("error")
Sets the css style to error

$(...).attr('name', 'value')
Sets the element's attribute name to value

$(...).clone()
Clones / Copies the content of the DOM node

$(...).click(clickHandler)
Assigns a event handler to an event $(...).each(function() { $(this).addClass('') }); For each element returned by a selection, apply a function (in this example a style class is added)

**Ajax with jQuery**

jQuery really makes it easy to send AJAX requests and deal with the response. The simplest example would be -

$.ajax({url: "address", success: funct1});

The handler function (funct1 above) gets the response text not the response object, the response can be XML, HTML, JSON or text. The function again can use jQuery to manipulate the DOM, for example display the response in a DIV.

**jQueryUI**

This blog would not be complete without a mention of jQueryUI - <http://jqueryui.com/> \- jQueryUI builds upon jQuery to provide an effects and interaction API as well as ready to use widgets. Using jQueryUI, it is a breeze to add behaviours like draggable and resizeable to spans/divs. The widgets include a highly customizable date picker among others. Feel free to visit the jQueryUI site to see the demos.

   [1]:  http://ajax.googleapis.com/ajax/libs/jquery/1.4.1/jquery.min.js
---
layout: post
header-img: img/default-blog-pic.jpg
author: balajidl
description: 
post_id: 198
created: 2007/04/04 16:05:58
created_gmt: 2007/04/04 14:05:58
comment_status: open
---

# On-demand validation using XHR

The idea is to validate the username/password as soon as the user enters the data into the form input field of the browser.

_The code snippets below will show how to do this using the XMLHttpRequest (**XHR**) object_. The XHR (or so called AJAX) helps to do this without making the webpage reload after hitting the server.

Well, you can use famous JavaScript libraries like Prototype, dwr or Dojo to do this. Using them will also reduce the code size. But... if you are interested in knowing how this is really done using XHR directly, then read on... :-)

We want to do something similar to the picture shown below:

![XHR][1]

**Steps involved:**

  1. Create the UI web page with form inputs in it.
  2. Write a jsp/servlet/asp or any server side code that can send an XML response back after checking the input username
  3. Write XHR based javascript code on the UI web page to call the server by passing the username, then display back the server response.

The source files containing the javascript code, sample html page and the jsp can be downloaded by clicking this [link][2]

Lets see how it can be done... 

  1. Creating a web page with form inputs field is pretty simple, let's skip it. 
  2. Server side code to check and send an XML response. (The server side code will take the input username from XHR, checks the name against the database and replies back with an XML response.  
The xml response for valid username will look like   
[xml] <?xml version="1.0" encoding="UTF-8"?> True [/xml] and the XML response for an invalid username will look like [xml] <?xml version="1.0" encoding="UTF-8"?> False [/xml] 
  3. The javascript code that initializes the XHR objects, calls the server side page with the username as inputQueryString, reads the response and then displays that to the web page is shown below. (This script is re-used for both the login page and the registration page) [js] 1 // These two global variables help for reusing the function validateUsername_XHR 2 var errMsg1 = "  
 _Username does not exist." 3 var errMsg2 = "  
 _Username already taken, try another." 4 5 // Initialize the onChange event of the input fields to call the XHR object for processing the validation logic. 6 // See the way how single call_validate and validateUsername_XHR was used for checking both username validation while logging in and registering. 7 setInit = function(username_login, username_reg) { 8 username_login.onchange = function(event) { 9 callValidate(username_login.value, errMsg1, "False", "login_msg"); 10 }; 11 username_reg.onchange = function(event) { 12 callValidate(username_reg.value, errMsg2, "True", "reg_msg"); 13 }; 14 } 15 16 // This seperate call function will be useful to show animated processing images while making XHR calls. 17 // Interested in knowing how to show 'processing' gif image, see my blog post [here][3] 18 // This also be used as a precheck function to decide calling XHR or not. 19 function callValidate(username, errMsg, status, err_msg_id) { 20 if (username != "") 21 if (username.length > 0) { 22 document.getElementById(err_msg_id).innerHTML = "  
Checking...."; 23 validateUsername_XHR(username, errMsg, status, err_msg_id); 24 } 25 } 26 27 // Gets the XHR object, loads and parses the XML from the response object 28 // status=False checks for username does exist or not for login purpose 29 // status=True checks for username avalilability for new registration 30 //sets the error message depending on the status 31 function validateUsername_XHR(username, errMsg, status, err_msg_id) 32 { 33 var XHRObject = getXHRObject(); 34 var url = "usernamevalidation_ajax.jsp?username=" + username; 35 XHRObject.open("POST", url, true); 36 //readystate will be 4 when the webpage is loaded completely. 37 XHRObject.onreadystatechange = function() { 38 if (XHRObject.readyState == 4 && XHRObject.responseText) { 39 if (XHRObject.responseText.charAt(0) == "<") { 40 try { 41 //parse the response xml dom and get the value of the node "username" 42 //it would be wiser to check error attribute as well. 43 var response = XHRObject.responseXML.getElementsByTagName("username")[0].firstChild.nodeValue; 44 if (response == status) document.getElementById(err_msg_id).innerHTML = errMsg; 45 else document.getElementById(err_msg_id).innerHTML = ""; 46 } 47 catch(e) { 48 document.getElementById(err_msg_id).innerHTML = ""; 49 } 50 } 51 else { 52 eval(XHRObject.responseText) 53 } 54 } 55 } 56 ; 57 XHRObject.send(null); 58 } 59 60 //Get the XMLHttpRequest from browser specific object. 61 function getXHRObject() { 62 var XHRobj = null; 63 try { 64 //Older IE's 65 XHRObj = new ActiveXObject("Msxml2.XMLHTTP") 66 } catch(e) { 67 try { 68 //Newer IE's 69 XHRObj = new ActiveXObject("Microsoft.XMLHTTP") 70 } catch(oc) { 71 XHRObj = null 72 } 73 } 74 if (!XHRObj && typeof XMLHttpRequest != "undefined") { 75 //Mozila/Safari 76 XHRObj = new XMLHttpRequest() 77 } 78 return XHRObj; 79 } 80 81 [/js]

Note: 

  * The response from the server can also be text or XHTML. It all depends on how exactly you want to parse the response using javascript.
  * If you are a beginner and just want to make use of this rich internet applications (AJAX), then it is suggested to start using well-grown javascript libraries like DWR/DOJO/Prototype/TIBCO UI, and so on. These kind of libraries has lot of features built already for you.

   [1]: http://xebee.xebia.in/wp-content/uploads/2007/04/dynamicValidation1.gif
   [2]: http://xebee.xebia.in/wp-content/uploads/2007/04/sourcefiles1.zip
   [3]:
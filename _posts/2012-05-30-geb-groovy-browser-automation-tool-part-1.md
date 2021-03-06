---
layout: post
header-img: img/default-blog-pic.jpg
author: gbansal
description: 
post_id: 14237
created: 2012/05/30 13:09:47
created_gmt: 2012/05/30 08:09:47
comment_status: open
---

# Geb: Groovy Browser Automation Tool - Part 1

There are many open source testing tools that claim to be very easy to use but they are NOT. However this tool, Geb (pronounced “jeb”) is really very easy as far as learning and usage is concerned. Tool has implemented most of the best known [strategies and patterns for Web GUI automation][1] and the [documentation][2] is just fantastic.

Test script creation is brought to a new level thanks to a number of features – Power of WebDriver, elegance of jQuery content selection, robustness of Page Object modelling and the expressiveness of the Groovy language. Awesome combination!!  Now let’s see how a Geb program looks like:

``` 
 import geb.Browser import org.openqa.selenium.firefox.FirefoxDriver

Browser.drive(new Browser(driver: new FirefoxDriver())) { go "http://www.twitter.com" assert title == "Twitter" } }.quit() 
 ```

There is a Browser class which has static method called drive(). In case we do not specify any parameter to drive() method then it uses HTMLUnit driver and runs the test cases. Otherwise as shown in the example we can pass required parameter to initialize instance of any of FirefoxDriver, InternetExplorerdriver, ChromeDriver and OperaDriver.

Functional testing of web applications has always been a challenge. Tests are expensive to write, painful to read, and relatively hard to maintain. Geb changed all of that. Geb’s Page Objects and Groovy DSL make tests readable to the point that they’re almost plain English. Beauty of Geb is that it can be integrated with many testing frameworks like JUnit, TestNG, Spock, Cucumber etc. In Part-1 we are covering JQuery locators, how using Navigator object we can navigate to all elements of a web page and the installation & usage of Geb.

**JQuery Locators**

As the name implies, jQuery focuses on queries. The core of the library allows you to find DOM elements using CSS selector syntax and run methods on that collection. jQuery offers a powerful set of tools for matching a set of elements in a document.

There is a dollar function that can be used anywhere to select content based on CSS selectors, attribute matchers and/or indexes. This $ function returns an object of Navigator class. A Navigator object is in someways analogous to the jQuery data type that represents one or more targeted elements on the page.

Lets take an example from www.twitter.com. Following is the screenshot and inner HTML of Sign Up form on twitter:

![Twitter Sign Up Form][3]

  
  
  
  
  
  
  


``` 
 <form action="https://twitter.com/signup" class="signup" method="post">

<div class="placeholding-input"> <input class="text-input" autocomplete="off" name="user[name]" maxlength="20" type="text" /> <span class="placeholder">Full name</span></div>

<div class="placeholding-input"> <input class="text-input email-input" autocomplete="off" name="user[email]" type="text" /> <span class="placeholder">Email</span></div>

<div class="placeholding-input"> <input class="text-input" name="user[user_password]" type="password" /> <span class="placeholder">Password</span></div>

<input value="front" name="context" type="hidden" /> <input value="c2bb63f5839426608c7c5e4610f08b576def8c3c" name="authenticity_token" type="hidden" />

<button type="submit" class="btn signup-btn"> Sign up for Twitter </button>

</form> 
 ```

Below are some sample JQuery locators that we can use to access html elements:

``` 


// match all 'div' elements on the page $("div")

// match the first 'div' element on the page which is related to 'Full name' $("div", 0)

// match all 'div' elements who have the class 'placeholding-input'

$("div.placeholding-input")

// match the second 'div' element with the class 'placeholding-input'

$("div.placeholding-input", 1)

// match the input element with a type attribute of value 'password'

$("input", type: "password")

// The parent of the first input

$("input", 0).parent()

// All inputs that are nested in a form

$("form").find("input")

// Please note that above-mentioned methods return Navigator objects that can be used to further refine the content. 
 ```

**Navigating the content**

Let’s see how we can traverse this html:

``` 
 import geb.Browser import geb.navigator.Navigator import geb.navigator.NonEmptyNavigator; import geb.spock.* import org.openqa.selenium.WebElement import org.openqa.selenium.firefox.FirefoxDriver

Browser.drive(new Browser(driver: new FirefoxDriver())) { go "http://www.twitter.com" assert title == "Twitter"
    
    
    // Navigator is a jQuery-style DOM traversal tool that wraps a set of WebDriver WebElements. Creates a new Navigator instance containing the elements matching the given CSS selector.
    Navigator allDivs = $(&quot;form.signup &gt; div&quot;)
    
    // Prints the size of list
    println allDivs.size()
    
    // Code to traverse from div to its children then getting WebElement(s) from Navigator object and printing the tag name of element
    for(Navigator div : allDivs) {
        Navigator children = div.children()
        for(Navigator child: children) {
            WebElement element = child.getElement(0);
            if(element.getTagName() == &quot;span&quot;) {
                println element.getText()
            } else {
                println element.getTagName()
            }
        }
    }
    

}.quit() 
 ```

In this example, we have created an instance of Navigator class containing the elements matching the given CSS selector. Now using this Navigator class we traversed from parent to its child Navigator objects to get required WebElement and then printed element’s tag name. See how easy it is and you have complete access each and every html element using Navigator class. You might be tempting to try your hands on Geb and for that here are the set-up instructions: 

  1. Install Groovy from <http://dist.codehaus.org/groovy/distributions/installers/windows/nsis/groovy-1.8.6-installer.exe> (There is currently an issue where you cannot have spaces in the path where Groovy is installed under windows.  So, instead of accepting the default installation path of "c:\Program Files\Groovy" you will want to change the path to something like "c:\Groovy")
  2. Install Groovy Plug-in in eclipse from <http://groovy.codehaus.org/Eclipse+Plugin>
  3. Enable the following option: Preferences->Groovy->Use monospace font for JUnit. This is important for Spock's condition output to be aligned correctly. If this doesn't work immediately, uncheck the option, press 'Apply', and check the option again.
  4. Download geb-core from <http://repo1.maven.org/maven2/org/codehaus/geb/geb-core/0.7.0/geb-core-0.7.0.jar>

   [1]: http://xebee.xebia.in/2012/03/30/web-gui-automation-tools-strategies-patterns/ (strategies and patterns for Web GUI automation)
   [2]: http://www.gebish.org/manual/0.7.0/
   [3]: http://xebee.xebia.in/wp-content/uploads/2012/05/Twitter.png (Twitter_SignUp)

## Comments

**[Gaurav Bansal](#9064 "2012-06-18 21:25:24"):** Hi Jaya, Thanks for your nice comment and good question. Geb is a Groovy based tool and we know Groovy and Java are really close cousins so you can definitely run all of your Java code using Geb however to leverage Groovy's awesome feature like its neat shortcut notations and its sensible defaults using which you can remove lot of boiler plate java code, you should start refactoring your code based on Geb's feature set. Regards, Gaurav

**[Jaya](#8972 "2012-06-06 12:31:15"):** Hi Gaurav, You have explained "Geb" tool very nicely in your blog. I am new to this tool and I have a question. I have my automation test suites written in Webdriver using java. Is it possible to migrate these tests to Geb? Thanks.

**[sonalgarg](#9423 "2013-05-30 12:06:43"):** Hi Gaurav,   I am new to Geb. I want to use selenium webdriver with geb. Please tell me selenium2 library for this.   Thanks in Advance


---
layout: post
header-img: img/default-blog-pic.jpg
author: Shankar Garg
description: 
post_id: 19075
created: 2014/11/16 10:40:25
created_gmt: 2014/11/16 05:40:25
comment_status: open
---

# Mobile App Automation: Android and iOS app automation via one Test Case using Appium

Agenda of this blog is to come up with a solution to use only one test case and execute it on both android and iOS. Lets first focus on why we would need something like this.

_First Lets try to answer why Organisations launch their apps on multiple platforms. Reason is:_ When an Organisation plans to launch an app, their main objective is to reach out to maximum users. So they decide to launch their apps on most popular platforms. iOS and Android are two most popular platform today.Thats the main reason why organisation simultaneously develop and launch apps on both platforms.

Now Lets focus on what it means for Mobile Dev and Mobile Test Teams.Dev teams can decide either of the following options:

  1. Start development of iOS and android separately and have two teams do this.
  2. Decide to use cross platform app development tools to write only one piece of code and deploy it on both android and iOS.  
Both options has their own pros and cons but one main benefit of option 2 is, it results in drastically reduced development time and budget. ( Some of you may not agree with this, but this is not the main agenda of this blog)

_**Now can Test Teams also follow option 2** ???_ So that test time and budget required for automating both apps is not 200%. This was the question that was hovering in mind from a long time. So i decided to give it a try to find a solution.

Lets try to understand whats needed to automate either a website or a mobile app. Irrespective of the tool used, we need to have these two facilities available to us: 

  1. How to find an element
  2. How to interact with an element 
So whats the Solution. Solution is to find a tool that can give us both of these for both the platforms. Thus only tool that can be used here is - _**[Appium][1]**(No points for guessing)_. The main advantage of Appium is that its supports multiple platforms: Android, iOS and firefox. Our solution lies in to understand how Appium supports multiple platforms. To understand "how Appium supports multiple platforms", we need to understand Appium's architecture. Please observe the below image carefully.

[caption id="" align="alignnone" width="1019"]![][2] Appium Architecture ( Source: http://unmesh.files.wordpress.com/2013/02/photo-1.jpg)[/caption]

Let me quote some text from Appium's Documentation: _"Appium is at its heart a webserver that exposes a REST API. It receives connections from a client, listens for commands, executes those commands on a mobile device, and responds with an HTTP response representing the result of the command execution. The fact that we have a client/server architecture opens up a lot of possibilities:"_

When we start appium, we pass some desired capabilities to appium session, which basically tells appium to start  either android or iOS session. For example, if i pass desired capabilities like "platformName" =="iOS", "deviceName"=="iPhone Simulator" or "platformName" =="Android", "deviceName"=="Android Emulator" then appium would start iOS and android session respectively.

Now when we run a test case, depending on which appium session we have started, all the commands passed to Appium Server will be converted to that platform's UI Automation native commands. This is how multiple platforms are supported.

Simple!! isn't it?? :-)

Now Lets see how Appium helps us in achieving the point 2 mentioned above. Since Appium extends Selenium Webdriver so interaction with elements is independent of the platform. For Example a button will be a button on android and iOS both and the function to interact with a button is .click() irrespective of platform. similarly for textbox, the function is .sendKeys() on both platforms.

So the only point left is point 1. Here Appium cant help us much because how we find elements is dependent on platform. On iOS, locators used are Name and Xpath and for Android locators used are id and ClassName etc. This issue was resolved with the help of framework where depending on which platform we want to automate, we loaded the required locators at run time.

_**We created two .properties files and all the locators specific to one platform were stored in one property file and specific to other were stored in other property file. Based on for which platform we want to execute the test for, locators from one of the feature files are loaded.**_

Lets go through the Framework and understand how we do this. Here is the tech stack for this framework:

Ant ( Build Tool), Java ( Programming Language), Appium(Automation Tool), TestNG(Test Framework) and Page Objects (Test Design Pattern)

  1. In build.xml, apart from defining normal TestNG specific tasks and targets, we have defined two targets run-test-android and run-test-iOS which not only runs android and iOS test suits but also sets the value of variable "Browser" to android and iOS respectively.

![build.xml1][3]

![build.xml2][4]

![build.xml3][5]

  1. DriverFactory.java, once ant target is executed in command line , as per TestNG, control will come to function tagged with @BeforeSuite. In this function, first i check the value of system property "Browser" that was being set in build.xml. if it is android or iOS, Appium Server server session is started accordingly.

![DriverFactory1][6] ![DriverFactory2][7]

  1. Next Step is Page Objects. We have defined a page object for each page of our mobile app. But there is a twist in the constructor. Here also in the constructor i check the value of system property "Browser" that was being set in build.xml. if it is android or iOS, locators from properties files are loaded accordingly.

![PageObject1][8] ![PageObject2][9]

   [1]: http://appium.io/
   [2]: http://unmesh.files.wordpress.com/2013/02/photo-1.jpg
   [3]: http://xebee.xebia.in/wp-content/uploads/2014/11/build.xml1_-300x242.png
   [4]: http://xebee.xebia.in/wp-content/uploads/2014/11/build.xml2_-300x158.png
   [5]: http://xebee.xebia.in/wp-content/uploads/2014/11/build.xml3_-300x79.png
   [6]: http://xebee.xebia.in/wp-content/uploads/2014/11/DriverFactory1-300x145.png
   [7]: http://xebee.xebia.in/wp-content/uploads/2014/11/DriverFactory2-300x207.png
   [8]: http://xebee.xebia.in/wp-content/uploads/2014/11/PageObject1-300x269.png
   [9]: http://xebee.xebia.in/wp-content/uploads/2014/11/PageObject2-300x116.png
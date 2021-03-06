---
layout: post
header-img: img/default-blog-pic.jpg
author: rnagpal
description: 
post_id: 6774
created: 2010/12/30 18:52:37
created_gmt: 2010/12/30 13:52:37
comment_status: open
---

# HTML5 vs Native mobile apps

Do you want to expand your web based solution to serve smart phone/tablet users? If  yes, then you will have to opt for either a native mobile application or a web application. This blog provides for an extensive comparative study of both options and explains why HTML5 would prove to be a better solution.

The platforms which currently dominate the smart phone/tablet market are : 1\. iOS (for iPhone) 2\. Android 3\. Blackberry OS 4\. Symbian

Lets first see what all is required to develop native mobile applications.

In order to develop a native mobile application, you will have to go through the following steps : ** **

**Development** Each platform has a different programming language tied to it, which in turn requires specific programming language skills, which are vastly different from web programming skills. This would require developers trained in that specific language to  be hired. ** **

**Deployment** Furthermore, platforms like iOS and Android  have an app store associated with them, and native applications can be downloaded only from their app stores. Also, uploading an application on the Apple App Store is a long drawn process (could take 1-2 weeks) . Although, the process for uploading your app on the android market  is a tad bit simpler. ** **

**Bug Fixing / Enhancement** Well if the above still seemed a cakewalk, then lets look at the pain involved with bug fixes/enhancements on native apps. For each bug that is fixed, the fix would have to be replicated on each platform in turn, and the application would have to be uploaded again on each app store (the vicious circle would repeat every time) ! The whole process usually would eat up more than a month of your precious time.

Now lets compare this whole process to a much simpler solution offered by HTML5.

HTML5 provides elements like header, nav, article, section, aside, and footer which make content more browser-readable. There are a lot of frameworks that are built upon HTML5 API's which rapidly speed up the development and reduces complexity. To name a few, **jQuery Mobile**,** iUI**: iPhone User Interface Framework, **jQTouch** are amongst the popular development frameworks for HTML5.

So HTML5 does great  when it comes to UI, but what about other things like GPS, performance, storing of data in mobile or servers pushing content to your apps?

For this, HTML5 provides API's which cater to these features. Here is the list of some of these which can help you develop applications as powerful as native mobile applications: 

  * **Geolocation API** helps you get the location of the user. This API in turn, uses the on-board GPS device.
  * **Web workers API** allows you to run multiple threads in browser.
  * **Web storage, Web Sql** allows you to store data at client side either in the for of key value pair or in relation database format.
  * **Web Socket** provides bi-directional, full-duplex communications channels, over a single Transmission Control Protocol (TCP) socket.
Using these API's you can now store offline data, run multiple threads and push content from server. These API's open a whole new arena where web applications can directly compete with native mobile apps.

Now, lets look at the different phases of HTML5 application development :

**Development** An HTML5 based web  application can run on phones across multiple platforms,which shortens development time and contributes to ease of development. Also, since HTML5 is a web based solution, the skill set required is almost same as that a normal web developer. An HTML 5 compliant browser is required to run HTML 5 applications, and most smart phones/tablets in the market come with one by default.

**Deployment** The deployment of HTML5 application is same as the normal web application. You can push the latest code to production whenever you want.

**Bug Fixing/Enhancement** Compared to the cycle for native apps, the enhancement cycles for an HTML5 app is very small as the application can be deployed immediately after the enhancement is made. Similar is the case for Bug fixes.

Lets side by side see the comparison of HTML5 and Native apps.

![][1]

Thus, there are pros and cons of both the techniques used for developing mobile app. If you want to build a simple internet based application just go for HTML5 , however if you want to develop a performance intensive application like games etc making native applications is always a better choice.

Some of the big players who are following this trend are <http://www.fliptrack.com/> <http://www.nextstop.com>

Related Articles

[Web Workers][2]

[Understanding WebSocket handshake][3]

[WebSocket communication using  jWebSocket][4]

   [1]: http://xebee.xebia.in/wp-content/uploads/2010/12/compare.jpg (compare)
   [2]: http://xebee.xebia.in/2010/11/02/multithreading-in-javascript-with-web-workers/
   [3]: http://xebee.xebia.in/2010/12/30/understanding-websocket-handshake/
   [4]: ../2010/10/05/web-socket-communication-using-jwebsocket/
---
layout: post
header-img: img/default-blog-pic.jpg
author: sohil
description: 
post_id: 5766
created: 2010/10/20 23:49:11
created_gmt: 2010/10/20 18:49:11
comment_status: open
---

# Android UI development tools

Today I had a pleasure of attending one of Google's Android Developer Conference by Rajdeep Dua ,Thanks to Rajdeep Dua I came to know some of android tools (by Google as well as third party not integrated with IDE)  available in the market to enhance UI application development.

  


Before we start looking through the list of tool. Please do have a look at the  [Android UI Guidelines][1]. If you publish your application on the android App market and would like your application to appear in the featured list you will have to meet the UI guidelines are drawn by above link.Hence do follow the UI guidelines while making your android application if you want Android market guys to consider your application in features list.

  


Okay , So lets start exploring the available tools. If you have just started learning android using eclipse IDE making a nice UI is not easy using the basic design mode.[DroidDraw][2] is the alternative to making good and fast graphical user interfaces. DroidDraw is available for (Windows , Mac , Linux) .It helps you to easily create the UI and generate the required xml file.If you would like to quick test your UI as how it looks on the android phone without integrating the code with you core application use [Androiddraw ][3]application.

  


Well that was droid draw to develop the UI faster. If you have gone through the UI Guidelines they are pretty strict to some extend. I will be difficult to cope up with it all the time.However we have a solution to that also. The next thing we are going to talk about is the[ Android UI Utils][4] . Android UI utils is the project currently present in google code. We are going to discuss two things Android Asset Studio and UI Prototyping Stencils.

  


[Android Asset Studio][5] is the Utility developed to help programmers create icons easily. Its a pretty handy tool to create different icons with all the variation like (hdpi , ldpi  ,mdpi) .Using this tool you can generate all kind of icons tab, menu launcher and notification icons.

  


Next is a UI Prototyping Stencil ([Pencil 1.2][6]) , its currently available as a add on in Firefox as well as a standalone .Its a free open source tool used to create quick GUI prototyping images.Pencil provides the rich set of android UI components avaliable for drag and drop to create a fast UI prototyping.

  


Apart from the above mentioned tools you should also use draw9patch to avoid inappropriate scaling of the images.[Draw9Patch][7] program is avaliable in android-sdk-windows\tools directory .A simple png file can be loaded into the application and after applying appropriate patches can be exported to get a NinePatch graphics.

  


Well that is not all, the last one is a Android NDK ([Native Development Kit][8]). Android NDK is a companion tool that you will have to download if you wish to build performance critical portions of the app in native code. Android NDK helps in embedding the components coded in Native code with Android SDK.

  


Well these are the few tools that i came across today that i wasn't aware. Please do leave a comment if you know few more.

   [1]: http://developer.android.com/guide/practices/ui_guidelines/index.html (UI Guidelines)
   [2]: http://www.droiddraw.org/ (DroidDraw)
   [3]: http://www.droiddraw.org/androiddraw.html (AnDroidDraw)
   [4]: http://code.google.com/p/android-ui-utils/
   [5]:  http://android-ui-utils.googlecode.com/hg/asset-studio/dist/index.html 
   [6]: http://pencil.evolus.vn/en-US/Home.aspx
   [7]: http://developer.android.com/guide/developing/tools/draw9patch.html
   [8]: http://developer.android.com/sdk/ndk/index.html

## Comments

**[Eric](#3134 "2010-11-04 01:57:11"):** Check out Corona from www.anscamobile.com, you can quickly develop native Android apps without using the Android SDK. Note also the same apps can be cross compiled to iOS.

**[Rajkumar](#5251 "2011-02-04 09:43:24"):** Thank you Sohil. Pencil project excites me.

**[Mariya](#5601 "2011-05-28 15:18:32"):** Hi I want to display more than one chart in android... So i am using afreechart jar file to generate the charts... I can generate the charts.. But i can see only one chart which is generated last.. That means the chart has overridden in the view activity.. I am using setContentView to view the charts.. In that i cant append.. So Please guide me... Thanks in advance, Maria

**[Rami](#5684 "2011-07-07 20:21:27"):** Bit old post, but I think it's reasonable to add http://crowdin.net as an essential localization tool

**[Jason](#6210 "2011-11-16 06:15:57"):** At Crank Software we are adding support for Android to our Storyboard UI Development Suite. You can see an early preview of it running on an Android Tablet here : http://cranksoftware.com/blog/?p=1074 Jason

**[Jeril](#6410 "2011-12-18 19:53:29"):** Hi, I tried using Pencil software. I have the Android items in my Collection also with the help of which I can create a quick good Android UI. Now how do I export it as an xml. Please help


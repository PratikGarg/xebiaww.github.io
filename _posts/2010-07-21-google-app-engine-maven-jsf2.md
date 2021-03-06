---
layout: post
header-img: img/default-blog-pic.jpg
author: rjaiswal
description: 
post_id: 4183
created: 2010/07/21 11:18:56
created_gmt: 2010/07/21 06:18:56
comment_status: open
---

# Google App Engine + Maven + JSF2

I have tried to put all the keywords in the title of this blog, hoping that it will turn some heads. Google App Engine (GAE) provides a great hosting platform for open source apps written in Java/Python. So if you have no qualms about hosting you Java applications on a public server then you have no better choice than GAE.

The only catch however is, that GAE does not support certain APIs (see the white-list here - <http://code.google.com/intl/de/appengine/docs/java/jrewhitelist.html>). This restriction makes it difficult to host certain apps / frameworks.

GAE provides the infrastructure, a modified Jetty server included in the GAE SDK (<http://code.google.com/appengine/downloads.html>) though which you can test your app. If it runs on the local GAE Jetty server then it will also run on GAE itself. The project structure that Google asks you to create for uploading an app is also quite typical (unless you use Ant) which brings me to my next point.

Maven is my favorite build tool, it would really take something for me to move on to Ant now. So I tried to create my application using Maven and then test it locally on GAE's Jetty and finally upload it to GAE. After a little search I found the Maven GAE plugin (<http://code.google.com/p/maven-gae-plugin/>).

Below is my pom.xml to make this plugin work - 

[code language="xml"] <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    
    
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
    &lt;groupId&gt;net.rocky&lt;/groupId&gt;
    &lt;artifactId&gt;rocky-gae-app&lt;/artifactId&gt;
    &lt;packaging&gt;war&lt;/packaging&gt;
    &lt;version&gt;1&lt;/version&gt;
    &lt;name&gt;rocky-gae-app&lt;/name&gt;
    &lt;url&gt;http://maven.apache.org&lt;/url&gt;
    
    &lt;repositories&gt;
        &lt;repository&gt;
            &lt;id&gt;java.net&lt;/id&gt;
            &lt;url&gt;http://download.java.net/maven/2&lt;/url&gt;
        &lt;/repository&gt;
    &lt;/repositories&gt;
    
    &lt;pluginRepositories&gt;
        &lt;pluginRepository&gt;
            &lt;id&gt;maven-gae-plugin-repo&lt;/id&gt;
            &lt;name&gt;maven-gae-plugin repository&lt;/name&gt;
            &lt;url&gt;http://maven-gae-plugin.googlecode.com/svn/repository&lt;/url&gt;
        &lt;/pluginRepository&gt;
    &lt;/pluginRepositories&gt;
    
    &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;javax.servlet&lt;/groupId&gt;
            &lt;artifactId&gt;servlet-api&lt;/artifactId&gt;
            &lt;version&gt;2.5&lt;/version&gt;
            &lt;scope&gt;provided&lt;/scope&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;javax.servlet.jsp&lt;/groupId&gt;
            &lt;artifactId&gt;jsp-api&lt;/artifactId&gt;
            &lt;version&gt;2.1&lt;/version&gt;
            &lt;scope&gt;provided&lt;/scope&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;com.sun.faces&lt;/groupId&gt;
            &lt;artifactId&gt;jsf-api&lt;/artifactId&gt;
            &lt;version&gt;2.0.2&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;com.sun.faces&lt;/groupId&gt;
            &lt;artifactId&gt;jsf-impl&lt;/artifactId&gt;
            &lt;version&gt;2.0.2&lt;/version&gt;
            &lt;classifier&gt;gae&lt;/classifier&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.glassfish.web&lt;/groupId&gt;
            &lt;artifactId&gt;el-impl&lt;/artifactId&gt;
            &lt;version&gt;2.2&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;junit&lt;/groupId&gt;
            &lt;artifactId&gt;junit&lt;/artifactId&gt;
            &lt;version&gt;4.8.1&lt;/version&gt;
            &lt;scope&gt;test&lt;/scope&gt;
        &lt;/dependency&gt;
    &lt;/dependencies&gt;
    
    &lt;build&gt;
        &lt;plugins&gt;
            &lt;plugin&gt;
                &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
                &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
                &lt;version&gt;2.0.2&lt;/version&gt;
                &lt;configuration&gt;
                    &lt;source&gt;1.6&lt;/source&gt;
                    &lt;target&gt;1.6&lt;/target&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;
            &lt;plugin&gt;
                &lt;groupId&gt;net.kindleit&lt;/groupId&gt;
                &lt;artifactId&gt;maven-gae-plugin&lt;/artifactId&gt;
                &lt;version&gt;0.6.0&lt;/version&gt;
                &lt;configuration&gt;
                    &lt;sdkDir&gt;/home/rockyj/01rocky/02apps/appengine-java-sdk-1.3.5&lt;/sdkDir&gt;
                &lt;/configuration&gt;
            &lt;/plugin&gt;
        &lt;/plugins&gt;
        &lt;finalName&gt;rocky-gae-app&lt;/finalName&gt;
    &lt;/build&gt;
    

</project> 
 ```

I have included the whole pom as there is hardly anything I can miss here. The GAE plugin is not in maven central so we need to include the plugin repository, the dependencies are pretty standard except one (which I will explain in a moment) and the plugin itself needs the configuration to know where you have the GAE SDK.

As I mentioned earlier, some classes are blacklisted on the GAE so your default jsf-impl.jar (Mojarra) wil not work on GAE. You will need a modified jar which you can download from here (<http://code.google.com/p/joshjcarrier/source/browse/trunk/Sun%20JSF%20GAE/jsf-impl-gae.jar>), if you have a Nexus repo you can install the modified jar there or install the jar in you local repository manually. Also as seen in the pom, for resolving EL (Expression Language) you need el-impl.jar, GAE does not support EL unlike Tomcat. 

To make JSF work you need the following entries in your web.xml -

[code language="xml"] <!-- Seems like GAE 1.2.6 cannot handle server side session management. At least for JSF 2.0.1 --> <context-param> <param-name>javax.faces.STATE_SAVING_METHOD</param-name> <param-value>client</param-value> </context-param> <!-- Recommendation from GAE pages --> <context-param> <param-name>javax.faces.PROJECT_STAGE</param-name> <param-value>Production</param-value> </context-param> <!-- Accommodate Single-Threaded Requirement of Google AppEngine --> <context-param> <param-name>com.sun.faces.enableThreading</param-name> <param-value>false</param-value> </context-param> 
 ```

Finally, in your appengine-web.xml (the GAE configuration file), you need the following entry - 

[code language="xml"] <sessions-enabled>true</sessions-enabled> 
 ```

That's it. Now you can work on your JSF2 application. To test, all you need to do is : **mvn clean gae:run**

For uploading the application, I would still recommend using the SDK provided script (otherwise you need a whole lot of other changes). What I do is - 

$HOME/01rocky/02apps/appengine-java-sdk-1.3.5/bin/appcfg.sh update /home/rockyj/01rocky/03workspace/scbcd/rocky-gae-app/target/rocky-gae-app/

This means run the GAE script with the param **"update"** and the path to your maven created target folder. If you have signed up for GAE, it will ask your username and password and your app will be uploaded to GAE.

## Comments

**[Uri](#68 "2010-08-12 03:00:19"):** Great write up. Thank you for putting this online. I didn't have suc a great experience with the maven age plugin which required some tweaking, especially with class paths when imported to eclipse. Q: can you comment on jsf/gae performance, cold start time etc. In comparison with other java frameworks?

**[Rocky Jaiswal](#88 "2010-08-16 08:26:06"):** Hi Uri, Thanks for your comments. From the little experience I have, I don't think that performance is that great for JSF apps on GAE. First, JSF itself is slow as compared to other F/Ws, then GAE also imposes some restrictions on threads (which can't be good for performance). App start time is good, no major problems there. But I would still recommend to look at other frameworks for GAE. JSF for production app on GAE can'e be good, its ok for prototypes etc.

**[notcourage](#3461 "2010-12-11 05:23:17"):** Couldn't get myfaces/appengine to work reliably until I disabled view saving encryption: org.apache.myfaces.USE_ENCRYPTION false javax.faces.STATE_SAVING_METHOD client

**[Daniel](#3068 "2010-10-27 15:11:08"):** There is away to use not modified implementation of mojarra... Works fine for me... Here is how to set it up. I wrote it on my page.. http://www.neverslair-blog.net/daniels-tips-and-tutorials/

**[Ara Minosian](#1581 "2010-09-14 04:45:02"):** Dear Rocky Thank you for great job. Do you now a way to use not modified implementation of mojarra? What do you think about: https://sites.google.com/a/wildstartech.com/adventures-in-java/Java-Platform-Enterprise-Edition/JavaServer-Faces/sun-javaserver-faces-reference-implementation/configuring-jsf-20-to-run-on-the-google-appengine Unfortunately this for Eclipse only. May be you can make new brilliant post.

**[Rocky Jaiswal](#1614 "2010-09-14 12:10:24"):** Hi Ara, Thanks for your feedback. Unfortunately due to Google App Engine restrictions the default Mojarra implementation cannot be used. In the article you have mentioned, which provides a nice intro to JSF2, there is also a link - "JavaServer Faces 2.0 and Google App Engine Compatibility Issues" at the bottom which explains this in detail. Finally, another advantage of using Maven is that the project can work seamlessly between Eclipse / Netbeans/ IntelliJ, as you are not relying on IDE but a standard Java tool (although for Eclipse you need the M2Eclipse plugin), so the project pom above should work on any IDE.


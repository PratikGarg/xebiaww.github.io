---
layout: post
header-img: img/default-blog-pic.jpg
author: anirudh.xebia
description: 
post_id: 17434
created: 2013/10/16 15:26:37
created_gmt: 2013/10/16 10:26:37
comment_status: open
---

# Implement single click deployment using Jenkins and DeployIt

**Problem statement :** Implement single click deployment using Jenkins and DeployIt **Tools used:** Jenkins CI Server, DeplyIt Plugin for Jenkins, Github plugin for jenkins, Maven for building artifact.

**Detailed use case :** Lets suppose we have our code in github in a public repository and we want that every fix which goes to "deployment" branch should get propagated to a dev environment automatically.

In order to get this set up working we would need to do: **1.)** Configure Jenkins with gitHub and deployIt Plugin. **2.)** Configure application placeholder and environment (including infrastructure) in which deployment would take place in DeployIt.  **PART 1: CONFIGURE DEPLOYIT**

First lets get the deployIt server up and create application and environment in it.

As per deployIt model, an application is a deployable which gets deployed into an environment (container). So we need application and the environment (comprising of infrastructure and properties files called as dictionaries)

Steps :

1.) Log into deployIt Server with admin privileges. 2.) Go to the Repository Tab -> Right click on Applications andselect new -> Application

Create a new application with name for example TaskMaster (This name would be used in the Jenkins deployIt plugin )

![Screen Shot 2013-10-16 at 4.10.56 PM][1]

3.) Create Infrastructure : point to a local tomcat instance, and name it "tomcat-local" Go to Infrastructure -> New -> Overthere - > Localhost

![Screen Shot 2013-10-16 at 4.14.35 PM][2]

4.) Define Tomcat using tomcat plugin and configure its path in infrastructure.

Right click on the created infrastructure -> New ->tc->tomcat we need to give value for home (path to Tomcat): /Users/xebia/apache-tomcat-7.0.42

![Screen Shot 2013-10-16 at 4.18.21 PM][3]

5.) Create Environment : Using this Infrastructure make a new environment

Name this Environment as Tomcat-Local and add the Infrastructure just created in it, and also add any dictionary (properties file)

![Screen Shot 2013-10-16 at 4.20.06 PM][4]

Last step would be to check the connection with the server, and make sure everything is configured properly.

After application and environment are successfully configured in deployIT, Lets configure Jenkins.

**PART 2: Configure Jenkins and plugins.**

**1.)** Set up Jenkins CI server : Either install Jenkins as standalone server using the installer [here][5]. Or just add the Jenkins WAR (download from here ) into your tomcat server webapps folder. Do the basic configurations required for Jenkins like set up workspace, JDK path, maven, git etc.

**2.)** Install github and deployIt plugin - Go to "Manage Jenkins" -> "Manage Plugins" -> click on available plugins tab and search for deployIt plugin, Install Deployit Jenkins plugin. Then search for gihub plugin and install GitHub plugin, it will install Jenkins Git plugin along with several other supporting plugins like SSH plugin etc. \- we would need to provide deployIt server URL and credentials in this section as well.

![Screen Shot 2013-10-16 at 3.37.32 PM][6]

3.) After we have got the required plugins and have configured Jenkins; lets make a new job, we will call it "Test-DeployIt-Deployment". ( Go to "New Job" link , select Build a free-style software project radio button )

4.) configuring project set up

On the page to configure new job, put the below settings :

4.1.) Project Name would be prepopulated, add a description if you want 4.2.) GitHub repository configuration \- give the URL of the project under "GitHub project" section. I gave it for my project on github - > https://github.com/anirudh83/TaskMaster1.1/

![Screen Shot 2013-10-16 at 4.23.35 PM][7]

4.3) Source Code Management

  * Go to section Source Code Management, and select git radio button. Give the repository URL for HTTPs as we would be doing a read only checkout. (https://github.com/anirudh83/TaskMaster1.1.git, for my case)

-Give the branch name to be build ( deployment branch in my case)

![Screen Shot 2013-10-16 at 4.24.43 PM][8]

4.4) Build Triggers : Click on check box saying : Build when a change is pushed to GitHub

4.5) Select Build Step - to build the checkout source code using maven, and add the maven goal to be executed

-Add clean package as the maven goal

4.6) Add Post-build action - Deploy with DeployIt (Make sure DeployIt plugin is configured in manage Jenkins section and it is running)

![Screen Shot 2013-10-16 at 3.44.55 PM][9]

4.7) Configure DeployIt to make a package, export it into the "application" in DeployIT and deploy it into the target environment configured in deployIT. See the configuration below :

![Screen Shot 2013-10-16 at 4.28.50 PM][10]

Once everything is configured we can save this job and trigger the build.

Make sure that version gets changed on every build. We can use $BUILD_NUMBER for the same.

We also need to provide Jenkins URL in our github repository in the hook so that it can trigger the build.

![Screen Shot 2013-10-16 at 4.31.03 PM][11]

Hope this post would help you get going in the path of contiuous delivery using Jenkins and DeployIt.

In the next blog post we will see how puppet plugin for deployIt can be used to provision the infrastructure.

   [1]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.10.56-PM.png
   [2]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.14.35-PM-1024x567.png
   [3]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.18.21-PM-1024x588.png
   [4]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.20.06-PM-1024x591.png
   [5]: http://jenkins-ci.org
   [6]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-3.37.32-PM-300x88.png
   [7]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.23.35-PM-1024x296.png
   [8]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.24.43-PM.png
   [9]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-3.44.55-PM-300x104.png
   [10]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.28.50-PM1.png
   [11]: http://xebee.xebia.in/wp-content/uploads/2013/10/Screen-Shot-2013-10-16-at-4.31.03-PM-1024x491.png
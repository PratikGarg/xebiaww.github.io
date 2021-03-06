---
layout: post
header-img: img/default-blog-pic.jpg
author: Rajdeep
description: 
post_id: 6531
created: 2010/12/29 16:23:58
created_gmt: 2010/12/29 11:23:58
comment_status: open
---

# Creating Seam-Hibernate Maven archetype

It all started when me and [Vaibhav][1] were getting our feet wet in a new project. The project was based on [Seam][2] and [Hibernate][3]. While trying to create sample projects involving Seam and Hibernate, we found most of the constructs were using [Ant][4] as build tool, thus limiting stuff like reporting, standard build lifecycle. Further they used seam-gen to generate startup projects. Also, available maven archetypes used EJB's (not suited to our purpose). So we thought of creating our own maven archetype based on Seam and Hibernate.

We went through the following steps to create the archetype: 

  * First we created the structure of the project that we wanted to be generated from the archetype.
  * We then used the command  **mvn archetype:create-from-project** (in root directory). An important file to be considered after this step is **archetype-metadata.xml** (found in target/generated-sources/archetype/src/main/resources/META-INF/maven) which contains meta information about the directories and files that will be included in the archetype. We can further enrich this file by marking required properties, giving them default values or even creating new properties. For example [sourcecode language="xml"] <requiredProperties> <requiredProperty key="groupId"> <defaultValue>com.xebia</defaultValue> </requiredProperty> </requiredProperties> [/sourcecode]
  * We now ran **mvn install **(after navigating to target/generated-sources/archetype) to finally generate the required archetype.
This method allowed us to use an IDE to generate the startup project and automatically convert it into the the archetype by running few commands.

The archetype uses seam, hibernate to provide the core functionality and spring,  dbunit are used in test cases. The project generated from archetype has user registration, login-logout functionality implemented. The structure of our project was :

[sourcecode language="xml"]

|--pom.xml `\-- src |-- main | |-- java | |`\-- com | | `\-- xebia | |`\-- seam | | |-- components | | | |-- Login.java | | | `\-- Register.java | |`\-- domain | | `\-- User.java | |-- resources | | |-- hibernate.cfg.xml | | |-- hibernate.properties | |`\-- seam.properties | `\-- webapp | |-- home.xhtml | |-- index.jsp | |-- login.xhtml | |-- META-INF | |`\-- MANIFEST.MF | |-- register.xhtml | |-- template.xhtml | `\-- WEB-INF | |-- components.xml | |-- faces-config.xml | |-- lib | |-- pages.xml |`\-- web.xml `\-- test |-- java |`\-- com | `\-- xebia |`\-- seam | |-- components | | `\-- LoginTest.java |`\-- databaseSetup | `\-- IDataMethods.java`\-- resources |-- dataset.xml `-- hibernate-test.cfg.xml [/sourcecode]

It was fun learning how maven can be used to do stuff that cannot be done with the help of other build tools which are pretty limited in the build scope. The archetype source is [here][5] , just unzip it and run mvn install to install the archetype in local repository.Then use **mvn** **archetype:generate -DarchetypeCatalog=local** to generate new project. We plan to release it to the maven repositories soon. Watch this blog for more updates.

   [1]: http://xebee.xebia.in/author/vaibhav sehgal/
   [2]: http://www.seamframework.org
   [3]: http://www.hibernate.org
   [4]: http://ant.apache.org/
   [5]: http://xebee.xebia.in/wp-content/uploads/2010/12/archetype.zip
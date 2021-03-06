---
layout: post
header-img: img/default-blog-pic.jpg
author: jgupta
description: 
post_id: 18879
created: 2014/08/30 16:17:04
created_gmt: 2014/08/30 11:17:04
comment_status: open
---

# Extending Selenium Grid capabilities

Selenium 2.xx has built-in Grid Functionality which provides support for distributed test execution i.e. multiple test automation scripts can be run at the same time on different machines running on different OS and browsers. Grid uses a DefaultCapabilityMatcher to match the machine(Node) requested by test script in the grid setup. Currently it uses only the Platform, Browser and its version to find the requested node. This can be extended to match the RAM or the System Type or any other Machine configuration to decide the node.  **Why?**

Suppose you have a grid with 100 nodes and you want to run your tests on 64-bit machines to check for specific errors or their performance on this system type. You have 100 proxies to deal with. You will have to go and check the system type of each, and unregister the nodes that have 32-bit or any other system type. This becomes very tedious job. To make this simple, we can extend the existing DefaultCapabilitMatcher class to add a capability to match the system type. Once this is done, grid will make sure to execute tests on the machines with the right capabilities.

**Setting up**

Open the [DefaultCapabilityMatcher][1] code and go through the logic of “matches” method. This is responsible for checking if the capability (OS, Browser and Version) requested by Test script is available with the grid. We can also write this method from scratch by directly Implementing the interface [CapabilityMatcher][2].

For now, we want to keep the existing code to match OS, browser and version as it is, and just add a feature to match the system type also. Let us see how we can get this going.

1.Add a new Java Project in Eclipse. Name it as “ExtendingGrid”. 2.Add a new Class “MyCapabilityMatcher”. 3.Add the latest selenium-server-standalone jar to the project class path. 4.Write the code to include capabilities for OS Type or installed RAM.

**Writing code to extend DefaultCapabilityMatcher**

``` 
 import org.openqa.grid.internal.utils.DefaultCapabilityMatcher; import java.util.Map;

public class MyCapabilityMatcher extends DefaultCapabilityMatcher { private final String nodeOS = "nodeOS"; boolean allCapabilitiesAvailability = false; @Override public boolean matches(Map<String, Object> nodeCapability, Map<String, Object> requestedCapability) { boolean defaultCapabilitiesAvailability = super.matches(nodeCapability, requestedCapability); if (! requestedCapability.containsKey(nodeOS)){ return defaultCapabilitiesAvailability; } else{ boolean nodeOSAvailability = nodeCapability.get(nodeOS).equals(requestedCapability.get(nodeOS)); allCapabilitiesAvailability = (defaultCapabilitiesAvailability && nodeOSAvailability); return allCapabilitiesAvailability; } } } 
 ```

**How it works:**

This class will first get the result of matching the original default capabilities and save it in defaultCapabilitiesAvailability. It will then check if the user has requested for the System Type also. If the test script has not asked for any specific system type, then this program will return the result of default capabilities availability. In case, the test script has asked for a system type Eg: 64-bit, then the code will reach the else block. Here it will check if the requested 64-bit OS is there in the available nodes, and return the result. To test the above code, we can write a test script requesting for 64-bit OS as given below.

**Writing Selenium Webdriver Test requesting 64-bit OS**

``` 
 import java.net.MalformedURLException; import java.net.URL; import org.openqa.selenium.remote.DesiredCapabilities; import org.openqa.selenium.remote.RemoteWebDriver;

public class sampleTest { public static void main ( String [] args){ DesiredCapabilities capabilities = new DesiredCapabilities(); capabilities.setBrowserName("firefox"); capabilities.setCapability("nodeOS", "64-bit"); RemoteWebDriver remoteWD = null; try{ remoteWD = new RemoteWebDriver( new URL("http://127.0.0.1:4444/wd/hub"), capabilities); }catch(MalformedURLException e){ e.printStackTrace(); } remoteWD.get("http://www.xebia.com"); //Add your code to test different things remoteWD.quit(); } } 
 ```

**Steps to run the test**

To use this in our Grid setup, we need to create a jar for the above project. Place it along with selenium standalone jar and add both of them to the classpath. Here is the sample command to start the hub:

**>java -cp *;. org.openqa.grid.selenium.GridLauncher -role hub -capabilityMatcher MyCapabilityMatcher**

This will start Hub at the default port 4444. We need to specify the capability while starting the node:

**>java -jar selenium-server-standalone-2.42.2.jar -role node -hub http://127.0.0.1:4444/grid/register -browser browserName=firefox,maxInstances=3,nodeOS=64-bit**

This will start the Node at the default port 5555. All the tests that request for 64-bit OS and firefox browser or just firefox browser will be routed to this node. To confirm the configuration, launch browser and try to access the grid at http://localhost:4444/grid/console#.

You should get something like this:

![Configuration][3]In the above page, you can see that the node is using MyCapabilityMatcher and has a 64-bit OS. Now you can run the tests and it will find the machine with 64-bit OS and run the test. If we modify the test script to ask for a 32-bit OS and if it is not available in the grid, then it will give an error saying the new session cannot find the Capabilities.

**Conlusion:**

Extending Grid capabilities will not only help to manage the nodes better, it will also give a better idea about what is going on during test script execution and help in debugging test failures.

   [1]: https://code.google.com/p/selenium/source/browse/java/server/src/org/openqa/grid/internal/utils/DefaultCapabilityMatcher.java?spec=svn95dd5912a5c741eacf1c0ea9a37abd494deb79d8&name=selenium-2.23.0&r=95dd5912a5c741eacf1c0ea9a37abd494deb79d8 (DefaultCapabilityMatcher)
   [2]: https://code.google.com/p/selenium/source/browse/java/server/src/org/openqa/grid/internal/utils/CapabilityMatcher.java (CapabilityMatcher)
   [3]: http://xebee.xebia.in/wp-content/uploads/2014/08/Configuration.png
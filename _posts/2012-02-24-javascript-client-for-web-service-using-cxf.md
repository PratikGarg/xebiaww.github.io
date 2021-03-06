---
layout: post
header-img: img/default-blog-pic.jpg
author: mshah
description: 
post_id: 12100
created: 2012/02/24 13:35:07
created_gmt: 2012/02/24 08:35:07
comment_status: closed
---

# JavaScript client for web service using CXF

Recently, I was looking for a Java Script client to test my Web Services. After my recent [findings on CXF][1], I found out that it can also create JavaScript clients using tools. Since existing tools had ANT support for generating JavaScript client and my current requirements was to use Maven. After not finding any such configuration, I dug thoroughly the cxf-codegen-plugin, I came up with below configuration for maven to generate JavaScript artifacts.  Below are the guidelines to generate JavaScript artifacts and integrate it with existing JavaScript client.

**Create a maven based web project (or use your existing web project) and modify pom.xml as below**

``` 
 <plugin> <groupId>org.apache.cxf</groupId> <artifactId>cxf-codegen-plugin</artifactId> <version>2.5.2</version> <executions> <execution>   
<configuration> <sourceRoot>${project.build.directory}</sourceRoot> <wsdlOptions> <wsdlOption> <wsdl>http://cxf_ws.cloudfoundry.com/sampleService?wsdl</wsdl> </wsdlOption> </wsdlOptions> </configuration> <goals> <goal>wsdl2js</goal> </goals> </execution> </executions> </plugin> 
 ```

In the pom mentioned above, sourceRoot tag in the configuration has been used to indicate directory to generate artifacts and wsdlOption has been used to mention the wsdl file location.

**Execute below maven command to generate JavaScript artifacts.**

mvn generate-sources

Once you run above command, a new JavaScript file gets generated at the mentioned destination directory, with the same name as wsdl definition name.

**Digging deeper into generated artifact.**

In brief about generated JavaScript artifact, There will be an operation function for every exposed web service operation and there will be a serialization and a deserialization functions for every operation function. The serialize function is used to convert the JavaScript object to an XML request format and the deserialize function converts the XML response to the corresponding JavaScript object. There will be a separate function which is used to map all these operation functions with their equivalent serialization/deserialization functions. This function is know as service function and gets generated by reading the service definition from the mentioned wsdl file in pom. The generated service function code starts with a comment given below.

``` 
 // Definitions for service: {http://test.com/}SampleCXFServiceService // Javascript for {http://test.com/}SampleCXFService 
 ```

**Integrating generated artifact with JavaScript client code.**

Modify your client side JavaScript to incorporate generated artifact changes. For example if wsdl definition name is SampleCXFService then a SampleCXFService.js file will get generated as an artifact. You need to include this SampleCXFService.js artifact in your client js. Other than that you also need to [download][2] CXF JavaScript utility file (cxf-utils.js) and include it in client JavaScript. This is a part of CXF distribution and can be found from apache-cxf-2.5.2-src\rt\javascript\javascript-rt\src\main\resources\org\apache\cxf\javascript location.

Add a JavaScript function (i.e. invokeService() ) to invoke the web service and store the result in a variable or use it directly(like in the snippet given below).

**Code snippet for client JavaScript:**

``` 
 <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.7.1/jquery.min.js"></script> <script type="text/javascript" src="cxf-utils.js"></script> <script type="text/javascript" src="SampleCXFService.js"></script>

<script language="JavaScript" type="text/javascript"> var SampleService = new test_com__SampleCXFService(); SampleService.url = "http://cxf_ws.cloudfoundry.com/sampleService";
    
    
    function invokeService()
    {
      var name = $('#inputTextId').val();
      &lt;!-- Operation function invocation by passing name as input parameter --&gt;
      SampleService.getMessage(successResponse, errorResponse, name);
    }
    
    &lt;!-- Function gets called for a successResponse. --&gt;
    function sucessResponse(response)
    {
        &lt;!-- Using response text to populate result on UI on success--&gt;
        $('#result').text(response.getReturn());
    }
    
    &lt;!-- Function gets called for an error. --&gt;
    function errorResponse(error)
    {
        alert(&quot;Error message is &quot; + error);
    }
    

</script> 
 ```

The first 2 lines are used to get the reference of web service using service function. The invokeService() function simply delegating call to operation functions. There are two callback functions also in client js. 

  * successResponse(response) : This function retrieves the response object in callback process and gets called after a successful response.
  * errorResponse(error): In case of web service invocation failure, this function retrieves the error message in the callback process and display that message in an alert.
**References:**

<http://cxf.apache.org/docs/overview.html> <http://cxf.apache.org/docs/wsdl-to-javascript.html>

   [1]: http://xebee.xebia.in/2012/01/25/what-made-me-to-choose-cxf-over-axis2/
   [2]: http://www.apache.org/dyn/closer.cgi?path=/cxf/2.5.2/apache-cxf-2.5.2.zip
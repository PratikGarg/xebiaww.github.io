---
layout: post
header-img: img/default-blog-pic.jpg
author: Gagan
description: 
post_id: 18284
created: 2014/04/30 09:58:51
created_gmt: 2014/04/30 04:58:51
comment_status: open
---

# Run Predictive Machine Learning algorithms on Hadoop without even knowing Mapreduce.

Data Scientists are very much familiar with working on tools like R, SAS etc. for them writing or converting algorithms into mapreduce is bit difficult. There are libraries such as Mahout is available which provides mapreduce implementation of many algorithms. you can not run your algorithm directly on a hadoop cluster. Before that you need to create a Data Model based on data and decide the values for some tweaking parameters and changing these parameters multiple time in hadoop job and running again and again is bit pain for a Data scientist, for a java developer it could be a fun. Data scientists can do Data modeling or model training in SAS/R very easily and efficiently.

Cascading comes up with a solution where Data scientists can design their data model in R and then they can export it into a PMML ( what is this? we will see in a moment) file and run cascading job which will run this algorithm over hadoop cluster. We need to write here minimal code. We need not to change the job if we are changing data model even if we are trying with different algorithm.

Let’s understand some of the terms we used above. 

  * **PMML** Predictive Model Markup Language is an XML-based file format developed by the Data Mining Group to provide a way for applications to describe and exchange models produced by data mining and machine learning algorithms [Wikipedia]. 
  * **Cascading** is a software abstraction layer for Apache Hadoop. Cascading is used to create and execute complex data processing workflows on a Hadoop cluster using any JVM-based language (Java, JRuby, Clojure, etc.), hiding the underlying complexity of MapReduce jobs. It is open source and available under the Apache License [Wikipedia]. 
  * **Cascading Pattern** is an extension to Cascading that provides various machine learning scoring algorithms and a utility for translating Predictive Model Markup Language (PMML) documents into applications on Apache Hadoop. Now you can deploy predictive models on to Hadoop or utilize the Cascading Pattern Java API to deploy your models or sophisticated ensembles [cascading.org] 

Here PMML helps us in defining data models with the help of any good predictive analytics tools and feed that PMML file to cascading job and you are done.

Let’s understand this with the help of very simple example.

Let’s say I want to run simple linear regression algorithm on iris dataset. iris is very popular dataset if you are not aware of this please read [here][1].

**Steps**

  1. Let’s say I want to predict sepal length based on the other parameters.

``` 


data(iris) str(iris) liearModel<-lm(Sepal.Length~.,data=iris) library(pmml) irisPMML<-pmml(liearModel) saveXML(irisPMML, "irisPMML.xml")


 ``` 
  2. Now pmml file is available in the working directory of R. You can check your working directory using following command ``` 
 getwd() 
 ``` 

  3. Create a new java project using maven as a build tool and add following dependencies. ``` 
 <repositories> <repository> <id>conjars.org</id> <url>http://conjars.org/repo</url> </repository> </repositories> <dependencies> <dependency> <groupId>cascading</groupId> <artifactId>cascading-core</artifactId> <version>2.5.1</version> </dependency> <dependency> <groupId>cascading</groupId> <artifactId>cascading-local</artifactId> <version>2.5.1</version> </dependency> <dependency> <groupId>cascading</groupId> <artifactId>cascading-hadoop</artifactId> <version>2.5.1</version> </dependency> <dependency> <groupId>cascading</groupId> <artifactId>cascading-xml</artifactId> <version>2.5.1</version> </dependency> <dependency> <groupId>cascading</groupId> <artifactId>pattern-pmml</artifactId> <version>1.0.0-wip-46</version> </dependency> <dependency> <groupId>cascading</groupId> <artifactId>pattern-core</artifactId> <version>1.0.0-wip-46</version> </dependency> <dependency> <groupId>cascading</groupId> <artifactId>pattern-hadoop</artifactId> <version>1.0.0-wip-46</version> </dependency> <dependency> <groupId>org.apache.hadoop</groupId> <artifactId>hadoop-core</artifactId> <version>1.2.1</version> <scope>provided</scope> </dependency> 
 ``` 
  4. Let’s write cascading job which reads PMML file and run the algorithm defined in the pmml file on input data over hadoop cluster. ``` 
 public class RunLinearRegressionPMMLExportFromR { public static void main(String args[]) throws IOException, ParseException { //getting input and output paths String inputPath = args[0]; String outputPath = args[1]; // some cascading classpath setting stuff Properties properties = new Properties(); AppProps.setApplicationJarClass(properties, RunLinearRegressionPMMLExportFromR.class); AppProps.setApplicationJarPath( properties, "/home/gagan/cascading-pmml/target/cascading-pmml-1.0-SNAPSHOT-packed.jar"); //getting pmml file name with path in job itself Options options = new Options(); options.addOption("pmml", true, "pmml file"); CommandLineParser parser = new PosixParser(); CommandLine cmd = parser.parse(options, args); if (!(cmd.hasOption("pmml"))) { HelpFormatter formatter = new HelpFormatter(); formatter.printHelp("RunLinearRegressionPMMLExportFromR.java", options); } // pmml file with path String pmmlWithPath = cmd.getOptionValue("pmml"); // creating cascading input/output Tap. Tap is a cascading terminology to define a source/sink. Tap inputTap = new Hfs(new TextDelimited(true, ",", "\""), inputPath); Tap outputTap = new Hfs(new TextDelimited(true, ",", "\""), outputPath); // generating flow definition FlowDef pmmlFlowDef = FlowDef.flowDef().setName("pmml-flow") .addSource("input", inputTap).addSink("output", outputTap); // PmmlPlanner. This will read pmml file and create a plan how cascading is going to create map reduce jobs for you data model. PMMLPlanner pmmlPlanner = new PMMLPlanner().setPMMLInput(new File( pmmlWithPath)); pmmlFlowDef.addAssemblyPlanner(pmmlPlanner); // creating the final flow. Flow flow = new HadoopFlowConnector(properties).connect(pmmlFlowDef); // We can write dot file to understand how cascading is writing the flow. flow.writeDOT("dot/flow.dot"); //run and complete the flow. flow.complete(); } } 
 ``` I have written detailed comments in code to explain what is happening there. 
  5. Build the above code and run using following command. ``` 
 hadoop jar <Jar File> <fully qualified class name > <input path> <output path> -pmml <pmml file with path> 
 ``` 
  6. After successfully running above hadoop job you can see find your predicted sepal lengths on the given iris input data. 

You can see the beauty of Cascading. We are able to run our linear regression algorithm over hadoop using mapreduce paradigm with a few lines of code.

Cascading Pattern following predictive modeling algorithms. 

  * Hierarchical Clustering
  * K-Means Clustering
  * Linear Regression
  * Logistic Regression
  * Random Forest Algorithm
Cascading team is working on providing support for following algorithms in their next release.

  * Neural Network
  * Support Vector Machine

   [1]: http://archive.ics.uci.edu/ml/datasets/Iris (Iris data set)
---
layout: post
header-img: img/default-blog-pic.jpg
author: pranjan
description: 
post_id: 4091
created: 2010/07/09 01:12:37
created_gmt: 2010/07/08 20:12:37
comment_status: open
---

# OSGi - Modular Development in Java

OSGi is standard/framework which can provide modularity to any java application. Its believed that there is lack of modularized development support in Java and some points sort of justify it. I’ll list few of them: **Low-level code visibility control** Java has very low level code visibility controls like private, protected, package, public. If you want any code in one package to view any code in another package then it needs to be declared as public which might expose certain functionality at other unwanted places. You either keep everything in same package or expose it through  public methods but both has some trade offs. **Not so intelligent class path concept** Applications are generally composed of various versions of libraries and components. The class path does not deals with different versions in an intelligent way, it simply returns the first version that it finds. Even if it did pay attention, there is no way to explicitly specify dependencies.  **Limited deployment and management support** Java’s support for managing deployments is also not admirable. There is no easy way in Java to deploy the proper transitive set of versioned code dependencies and execute your application. There is no obvious way to evolve your the application and components.The only way to achieve such requests is to use class loaders, which are low level and error prone. **Enough of cursing java. Now, lets see, what OSGi can provide.**

  * It can help in satisfying code dependencies before executing code.
  * It makes sure that the set of dependencies are consistent with respect to required versions.
  * Package application as logically independent JAR files and be able to deploy only those pieces which are actually need for a given installation.
  * Package application as logically independent JAR files and also to declare which code is accessible from each JAR file.OSGi enables a new level of code visibility for JAR files that allows to specify what is and what is not visible externally.
  * Define an extensibility mechanism for your application, like a plug in mechanism.

**So, What is OSGi:** OSGi is a industry standard, defined by OSGi alliance to address the lack of support of modularization in Java. It introduces a service oriented programming model, known as “SOA in VM”. Modularity is defined as the partition/division of code into parts having separate concerns. **OSGi overview** The OSGi Service Platform is composed of two parts: the OSGi framework and OSGi standard services . The framework is the run time that implements and provides OSGi functionality. The standard services define reusable APIs for common tasks, such as Logging and Preferences. The OSGi specifications for the framework and standard services are managed by the OSGi Alliance (http://www.osgi.org). It is an industry backed non-profit corporation. **The OSGi Framework** It is the application's execution environment. It provides a well-defined API to program against. The specification also enables the creation of multiple implementations of the core framework; there are a handful of well-known open source projects, such as Apache Felix, Eclipse Equinox, and Knopflerfish. **_The OSGi framework is basically divided into three layers:_** **_ Module layer_** – this layer is concerned with packaging and sharing code. **_Lifecycle layer_** – this layer is concerned with providing run-time module management and access to the underlying OSGi framework. _** Service layer**_ – this layer is concerned with interaction and communication among modules, specifically the components contained in them. **Module layer** The module layer defines the OSGi module concept, called a bundle, which is simply a JAR file with extra metadata about the jar. A bundle contains class files and other resources. Bundles are the logical modules that combine to form a given application. Bundles empower you to explicitly declare which contained packages are externally visible. **Lifecycle layer** The lifecycle layer defines how bundles are dynamically installed and managed in the OSGi framework. the lifecycle layer defines the bundle lifecycle operations (e.g., install, update, start, stop, and uninstall). These lifecycle operations allows to dynamically administer, manage, and evolve applications in a managed way. **Service layer** The main features are the service-oriented publish, find, and bind : service providers publish their services into a service registry, while clients search the registry for services to use. OSGi services are local to a single VM, so, OSGi is also referred as “SOA in a VM”. OSGi services are simply Java interfaces representing a contract between service providers and clients.This makes the service layer light, since service providers are simple Java objects accessed via direct method invocation. **So, to use OSGi:**

  * Design applications as services exposed and clients implemented (normal interface driven design).
  * Package service provider and client components into separate JAR files, adding proper meta data in each jar file.
  * Start the OSGi framework.
  * Install and start all of your component JAR files.

In the OSGi world, services will publish themselves in the service registry and clients will look up available services in the registry. I will try to write out a basic example of OSGi implementation soon.
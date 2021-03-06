---
layout: post
header-img: img/default-blog-pic.jpg
author: vagrawal
description: 
post_id: 17273
created: 2013/10/08 08:31:53
created_gmt: 2013/10/08 03:31:53
comment_status: open
---

# OSGi : Modular Java

**What is OSGI ?**

Open Source Gateway Initiative (OSGi) service platform is a technology which enables the complex Java applications to be modularized easily and effectively. OSGi also known as Dynamic Module System for Java, helps to divide an application into different layers (bundles) ensuring separation of concerns is met effectively. As a continuation of its modularity support, it introduces service-oriented programming model (SOA), to help you clearly separate the interface from implementation. The OSGi Service Platform is essentially an industry standard defined to specifically address the lack of support for modularity in the Java platform.

**What do we mean by lack of modularity support in Java?**

Let’s first understand what we mean by modularity, modularity is more or less dividing an application into logical parts representing separate concerns. Although Java provides various access modifiers to control visibility (such as public, protected, private and package), but these modifiers can control only very low level object-oriented encapsulation and not logical system partitioning. Java also has a concept of package which is typically used for partitioning code. For code to be visible from one package to another it must be declared public (or protected if using inheritance). But this means any dependencies among the packages must be exposed as public, which makes them accessible to everyone else too.

Let’s understand this problem with a simple example

``` 
 A)

package com.vinsworld.jungle;

public interface Animal { public void makeSomeNoise();

}

B) package com.vinsworld.jungle.impl;

import com.vinsworld.jungle.Animal;

public class Lion implements Animal {
    
    
    public void makeSomeNoise() {
        System.out.println(&quot;Ghrrrrrrr&quot;);
    }
    

} C) package com.vinsworld.jungle.test;

import com.vinsworld.jungle.Animal; import com.vinsworld.jungle.impl.Lion;

public class AnimalTest {
    
    
    public static void main (String []args){
    
        Lion lion = new Lion();
        animal.makeSomeNoise();
    }
    

} 
 ```

Now here if such a code(A and B) is a part of some API then author will always want the client (C) to interact with the application via Animal interface, but nothing is stopping client from creating a new Lion object using its public constructor as is done in (C).

We can argue here that this could have been achieved in a better way by creating a protected constructor and then using factory pattern etc in (B) or by even not having the unnecessary packaged structure, all this can be very well true in this trivial example. But in the real world application package structure together with the access modifier turns out to be very basic tool for ensuring API coherency. Because supposedly private implementation details can be accessed by third-party developers, we need to worry about changes to private implementation signatures as well as to public interfaces when making updates. This means in Java we will always be forced to decide between two of the followings

1)       Club unrelated classes in a same package to avoid exposing non-public APIs.

2)       Create logical structure properly by using multiple packages at the expense of exposing non-public APIs so they can be accessed by classes in different packages.

Another big problem which impacts Java modularity is maintaining the versions of the jars and dependencies amongst them. The main culprit for this is class path concept. For ex: - if multiple version of same jar is present in the class path, class path will not pay any attention to the version; it will just return whatever it finds first. This may lead to errors like NoSuchMethodError.

Also there is no way in Java to maintain the modules at runtime for ex: - If you have a modularized application with the DAO layer and now if we want to update the DAO layer, we have to bring down the complete application and then only it can be updated.

**How OSGi can help ?**

The answer lies in OSGi container. OSGi container is the set of services and contracts build using OSGi specification. Some of the popular opensource OSGi containers available are

Equinox

Knopflerfish

Apache Felix

Let’s see how OSGi container helps to overcome Java modularization hurdles effectively, by using a sample HelloWorld sort of application.

I will be using Eclipse Equinox to create this module (bundle).In OSGi, software is distributed in the form of a bundle. Bundle is a physical unit of modularity in the form of jar containing code, resources and metadata. In this sample application we will create two bundles Helloworld and DateService. To create a Bundle using Eclipse please follow these steps.

1)       File > New > Plug-in Project

2)       In the Plug-in project dialog box

Keep Project Name as follows: com.sample.helloworld

Keep target platform as “an OSGI framework” standard.

3)       Click _next_ and for everything else just keep the default values.

As soon as wizard will be over, Eclipse will create two files Activator and manifest.mf.

``` 
 com.sample.helloworld.Activator.java package com.sample.date;

import org.osgi.framework.BundleActivator; import org.osgi.framework.BundleContext;

public class Activator implements BundleActivator {

public void start(BundleContext context) throws Exception { System.out.println("Hello World!!"); }

public void stop(BundleContext context) throws Exception { System.out.println("Goodbye World!!"); } } 
 ```

If we want the bundle to be notified when the bundle is started or stopped by a container we need to implement BundleActivator interface.

Please make sure these guide lines are followed while implementing the same

a)       BundleActivator class must have a public constructor that takes no parameter. OSGi container may need to create the object of the BundleActivator by using Class.newInstance().

b)      Start and stop method will be called by the container during the start and stop of the bundle. Developer can use this opportunity to initialize/destroy the resource objects to be used by bundle for ex:- Database connection.

**Manifest.mf**

The MANIFEST.MF file acts as deployment descriptor for your bundle. The format for this file is the same as that of a normal JAR file, so it consists of a set of headers with values. The OSGi specification defines a set of headers that you can use to describe your bundle to the OSGi container. The typical Manifest.mf will look as follows.

``` 
 Manifest-Version: 1.0 Bundle-ManifestVersion: 2 Bundle-Name: Helloworld Bundle-SymbolicName: com.sample.helloworld Bundle-Version: 1.0.0.qualifier Bundle-Activator: com.sample.helloworld.Activator Bundle-Vendor: SAMPLE Import-Package: org.osgi.framework;version="1.3.0" 
 ```

Let’s take a closer look on each of these headers

**Bundle-ManifestVersion: ** Tells OSGi container that this bundle follows the rules of the OSGi specification. A value of 2 means that the bundle is compliant with OSGi specification Release 4; a value of 1 means that it is compliant with Release 3 or earlier.

**Bundle-Name:** Defines a short, human-readable name for the bundle.

**Bundle-SymbolicName: **Specifies a unique, non-localizable name for the bundle. This is the name you will use while referring a given bundle from other bundles.

**Bundle-Version**: Specifies the version of the bundle.

**Bundle-Activator**: Specifies the name of the optional listener class to be notified of bundle start and stop events. In the above example the value of this header is com.javaworld.sample.Activator.

**Bundle-Vendor: **Contains a human-readable description of the bundle vendor.

**Import-Package:** Defines imported packages for the bundle. This is the header which helps to attain dependency management. We will see more about this as we proceed further.

Let’s run our first bundle using OSGi container of eclipse. Here are the steps

1)       Click on Run > Run configuration

2)       In “Create, Manage and Run configuration window” dialog box name the configuration as something meaningful.
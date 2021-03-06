---
layout: post
header-img: img/default-blog-pic.jpg
author: smehra
description: 
post_id: 6962
created: 2010/12/31 16:06:36
created_gmt: 2010/12/31 11:06:36
comment_status: open
---

# Speech to Text Conversion in Java

This blog aims at creating a project for Speech-to-text conversion (Speech Recognition) on **JAVA** by using **Eclipse IDE**, **Maven** and a speech recognition system written entirely in Java language called **Sphinx-4**.

Steps for Speech-to-text converter project setup: 1\. Create a simple Maven project. 2\. Add "**http://repository.opencastproject.org/nexus/content/groups/public/**" to your set of repositories in pom.xml 3\. Add Sphinx-4 dependency in pom.xml of your project.

Your pom.xml should look like the following:

[sourcecode language="xml"] <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"> <modelVersion>4.0.0</modelVersion>

<groupId>com.xebia</groupId> <artifactId>speech-to-text-converter</artifactId> <version>0.0.1-SNAPSHOT</version> <packaging>jar</packaging>

<name>speech-to-text-converter</name> <url>http://maven.apache.org</url>

<properties> <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> </properties>

<repositories> <repository> <id>sphinx-repository</id> <name>Nexus Repository</name> <url>http://repository.opencastproject.org/nexus/content/groups/public/</url> </repository> </repositories>

<dependencies>   
<dependency> <groupId>cmu.sphinx</groupId> <artifactId>sphinx4</artifactId> <version>4.0</version> </dependency> </dependencies>

</project> [/sourcecode]

We will first write the java code for the speech recognizer. Create a class and name it "**HelloWorld.java**". Code it as:

[sourcecode language="java"] package com.xebia.speech-to-text-converter;

import java.io.IOException; import java.net.URL;

import edu.cmu.sphinx.frontend.util.Microphone; import edu.cmu.sphinx.recognizer.Recognizer; import edu.cmu.sphinx.result.Result; import edu.cmu.sphinx.util.props.ConfigurationManager; import edu.cmu.sphinx.util.props.PropertyException;

/*_ * A simple HelloWorld demo showing a simple speech application built using Sphinx-4. This application uses the Sphinx-4 * endpointer, which automatically segments incoming audio into utterances and silences. _/ public class HelloWorld {
    
    
    public static void main(String[] args) throws IOException, PropertyException, InstantiationException {
        ConfigurationManager cm;
    
        if (args.length &gt; 0) {
            URL url = new URL(args[0]);
            cm = new ConfigurationManager(url);
        } else {
            cm = new ConfigurationManager(HelloWorld.class.getResource(&quot;helloworld.config.xml&quot;));
        }
    
        Recognizer recognizer = (Recognizer) cm.lookup(&quot;recognizer&quot;);
        recognizer.allocate();
    
        // start the microphone or exit if the program if this is not possible
        Microphone microphone = (Microphone) cm.lookup(&quot;microphone&quot;);
        if (!microphone.startRecording()) {
            System.out.println(&quot;Cannot start microphone.&quot;);
            recognizer.deallocate();
            System.exit(1);
        }
    
        System.out.println(&quot;Say: (Good morning | Hello) (( Winfred | Evandro | Paul | Philip | Will )&quot;);
    
        // loop the recognition until the program exits.
        while (true) {
            System.out.println(&quot;Start speaking. Press Ctrl-C to quit.\n&quot;);
    
            Result result = recognizer.recognize();
    
            if (result != null) {
                String resultText = result.getBestFinalResultNoFiller();
                System.out.println(&quot;You said: &quot; + resultText + '\n');
            } else {
                System.out.println(&quot;I can't hear what you said.\n&quot;);
            }
        }
    }
    

} [/sourcecode]

To set it, up and running, we will write a grammar file for the input expected by the recognizer.

Name it "**hello.gram**" and place it in the same package as HelloWorld.java file. Grammar file is always saved with **.gram** extension. The name of this file is used in the configuration file.

The grammar file will take the following form:

[sourcecode language="java"]

# JSGF V1.0;

/__ _ JSGF Grammar for Hello World example _/ grammar hello;

public <greet> = (Good morning | Hello) <name>; <name> = ( Winfred | Evandro | Paul | Philip | Will ); [/sourcecode]

According to this grammar, the system can recognize the following if spoken: 1\. Good morning Winfred 2\. Good morning Will 3\. Hello Winfred 4\. Hello Philip or anything starting with a <greet> and followed by a <name> from the options available in grammar.

We also need to write a configuration file for the recognizer to configure various Sphinx-4 components. Name this file as "helloworld.config.xml" and place it in the same package as your HelloWorld.java file. Remember that the name of this file will be used in code for HelloWorld.java. You can find the code for the configuration file here: [helloworld.config.xml][1].

After performing the above steps, you can simply execute HelloWorld.java as Java Application, speak in the microphone any text in the grammar format and you will find your speech converted to text and displayed on the command prompt.

Sphinx-4 uses a file **cmudict.0.6d** found in **WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.jar** at location **/edu.cmu.sphinx.model.acoustic.WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.Model!/edu/cmu/sphinx/model/acoustic/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz/dict/cmudict.0.6d** as its dictionary. This dictionary file contains words along with their pronounciation e.g. WINFRED W IH N F R IH D will be the entry for the word Winfred in this file. You can also add your set of words in this file in the same format, i.e. word followed by its pronounciation, package it and replace the original WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz.jar and you are ready to go!

These newly added words will now be recognized by Sphinx-4, provided, you also change the grammar for these words.

   [1]: http://xebee.xebia.in/2010/12/31/speech-to-text-conversion-in-java-2/helloworld-config/

## Comments

**[MUHAMMAD Junaid](#5350 "2011-03-15 23:27:36"):** Hi, I am Junaid and I want to develop User Independent Automated Speech recognition System for Urdu Language as my BS-Software Eng. final year project any 1 interested and willing to help me in this please contact @ http://twitter.com/#!/SydJunaidShah http://www.facebook.com/sydjunaidshah or EMail me @ sydjunaidshah@yahoo.com BEST REGARDS Jojo

**[amol](#5269 "2011-02-05 20:11:03"):** I am Amol Dudvadkar from Mumbai. I am doing engineering in I.T. branch from Vidalankar College. I am doing final year project named "Voice Command Module for OpenOffice.org Writer Application". For that we are going to use Sphinx4. In this regard I need your little help and some inputs. Here is my mail-id : amoldudvadkar@gmail.com I will be obliged for your help :-)

**[sumit](#5364 "2011-03-26 19:42:24"):** please give me your e-mail i also work on same project. need some info. about that.

**[Shruti](#5394 "2011-04-04 12:25:17"):** Hi all, You can post your queries in the same thread.

**[heena](#4682 "2011-01-03 11:54:01"):** excellent job my dear!!

**[Raman](#4683 "2011-01-03 13:57:14"):** Thanks for sharing... was really helpful to do a Jump start..

**[Dishant](#5416 "2011-04-07 19:46:59"):** Can you please write the code where Microphone can recognize any word a user says? Please, i have to submit minor project on speech-to-text converter on next thursday......Please....!

**[Shruti Mehra](#5417 "2011-04-08 09:24:16"):** The code for the microphone is already in the blog. In order to get any word recognized, you will have to make changes in "cmudict.0.6d" file. Which is the dictionary file with each row containing word<\--->pronounciation pair. The information about the same is also already present in the blog.

**[yasir](#5499 "2011-04-25 10:48:29"):** brilliant well written blog can you plz discuss the opening parts in any other thread because setting up maven and eclipse etc is 1 hell of a job it self ......i have just started working on this as my final project i will be really thankfull to you if you can help me out in start ...my email id is yasir@tahirgrouppak.com

**[Dishant](#5421 "2011-04-09 14:58:34"):** Please explain these steps in complete detail.... I have never gone through such things.... 1\. Create a simple Maven project. 2\. Add "http://repository.opencastproject.org/nexus/content/groups/public/" to your set of repositories in pom.xml 3\. Add Sphinx-4 dependency in pom.xml of your project. & one more query... is this a web application project or desktop application?

**[Shruti Mehra](#5455 "2011-04-12 08:56:17"):** These steps are the basics of **Maven** which is a build tool. The current code outputs the result on command line. If you are unaware of Maven then you can check the official site for Sphinx 4 which uses Ant as its build tool. The explanation of Maven basics is beyond the scope of this blog and this comment thread.

**[Dishant](#5462 "2011-04-13 16:27:48"):** I have added dictionary words in hello.gram file so as to get recognized each & every word. But the exception results like this: Exception in thread "main" java.lang.RuntimeException: Allocation of search manager resources failed Is it problem of heap space or the large size of hello.gram?

**[Shruti Mehra](#5465 "2011-04-14 08:52:25"):** Modifying hello.gram alone is not the solution to your problem. It is just a grammar file, i.e. the format/order in which you expect your words to be recognized. Just like the grammar in the blog expects a followed by a . These are placeholders substituted by concrete values. The grammar file checks words against the dictionary file which is where you need to add the word with its pronunciation. Map it to something like: Teachers mark attendance in a register against a student who actually exist. In your case you are checking for words which do not exist in the dictionary.

**[Shruti Mehra](#5803 "2011-08-02 14:49:17"):** Make sure the words you are speaking are in the same format as your grammar file. It might give uncertain result in case of noisy environment too.

**[mohsin](#5508 "2011-04-26 18:07:29"):** hi shruti, Nice code, But i want to implement the speech recognition in mobile,like if user speak something then mobile will detect the particular word and do some task....i just know that i have to implement jsr113 for this....but i don't know how to implement it....

**[Suresh Jamma](#5802 "2011-08-02 11:06:40"):** Hi, I tried this example in NetBeans IDE as maven project. I am able to Run HelloWorld but my Speech is not recognizing . what might be the reason. For confirmation of my MIC,Audio i recorded simple wav file in recorder able to do. Thanks in Advance.

**[mukesh](#5582 "2011-05-19 23:56:40"):** hi... wat r doze 1st 3 pnts written by u some maven prct,link,sphinx-4 in .xml and all.. im nt getng dose....... plz help me out by explainin abt dem in detail......plz do mail me......... ma prjct got stukd and i nedd ur help(i have 12 more days only..........:/:/:(

**[nikunj](#5848 "2011-08-19 13:13:00"):** Please explain these steps in complete detail…. 1\. Create a simple Maven project. 2\. Add “http://repository.opencastproject.org/nexus/content/groups/public/” to your set of repositories in pom.xml 3\. Add Sphinx-4 dependency in pom.xml of your project. I Cab used Eclipse but i can't unserstan pls help me pls explain me detail & if possible so pls give me your email or facebook id

**[Problems executing HelloWorld](#5956 "2011-09-29 19:05:35"):** Hello I have writen the same project that you have used before and when I execute it I have the following problem : Exception in thread "main" Property exception component:'wsjLoader' property:'location' - Can't locate resource:/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz edu.cmu.sphinx.util.props.InternalConfigurationException: Can't locate resource:/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz Have you ever had a similar problem ? I have invested on it but I have not found any solution. Thanks in advance Gorka

**[Sagar](#6435 "2011-12-22 18:51:20"):** hello i m got this error Exception in thread "main" java.lang.NullPointerException at edu.cmu.sphinx.util.props.SaxLoader.load(SaxLoader.java:64) at edu.cmu.sphinx.util.props.ConfigurationManager.loader(ConfigurationManager.java:383) at edu.cmu.sphinx.util.props.ConfigurationManager.(ConfigurationManager.java:115) at com.xebia.HelloWorld.main(HelloWorld.java:27) how can i solve it ??

**[Sagar](#6436 "2011-12-22 18:52:11"):** hello i m got this error Exception in thread "main" java.lang.NullPointerException at edu.cmu.sphinx.util.props.SaxLoader.load(SaxLoader.java:64) at edu.cmu.sphinx.util.props.ConfigurationManager.loader(ConfigurationManager.java:383) at edu.cmu.sphinx.util.props.ConfigurationManager.(ConfigurationManager.java:115) at com.xebia.HelloWorld.main(HelloWorld.java:27) how can i solve it..??

**[Ajitesh](#6452 "2011-12-27 20:44:45"):** Hello Mam, I am developing a speech recogniser and translater in java. So i am using sphinx4 for the purpose. I am able to run all the demos. I am very new in sphinx. I am stuck at how to use dictionary in my application so that i can get access to variety of words. I also need a little help in training. The main problem is how to recognise a large variety of words because grammar file cant be created for large variety of sentences. So How do i recognise those sentences? please throw a light on this

**[Sandeep](#6039 "2011-10-20 17:09:17"):** Do you have any original work you have just copied and pasted HelloWorld.java and all the above from sources available for download at "cmusphinx.sourceforge.net/sphinx4/" whats innovative in it.

**[Anikesh haran](#6076 "2011-10-30 18:13:02"):** hi..... mam i trying this above code but i got Exception as shown below: Exception in thread "main" Property exception component:'wsjLoader' property:'location' - Can't locate resource:/WSJ_8gau_13dCep_16k_40mel_130Hz_6800Hz edu.cmu.sphinx.util.props.InternalConfigurationException at edu.cmu.sphinx.util.props.ConfigurationManagerUtils.getResource(ConfigurationManagerUtils.java:483) at edu.cmu.sphinx.linguist.acoustic.tiedstate.Sphinx3Loader.newProperties(Sphinx3Loader.java:265) at edu.cmu.sphinx.util.props.PropertySheet.getOwner(PropertySheet.java:475) at edu.cmu.sphinx.util.props.PropertySheet.getComponent(PropertySheet.java:285) at edu.cmu.sphinx.linguist.acoustic.tiedstate.TiedStateAcousticModel.newProperties(TiedStateAcousticModel.java:102) at edu.cmu.sphinx.util.props.PropertySheet.getOwner(PropertySheet.java:475) at edu.cmu.sphinx.util.props.PropertySheet.getComponent(PropertySheet.java:285) at edu.cmu.sphinx.linguist.flat.FlatLinguist.setupAcousticModel(FlatLinguist.java:278) at edu.cmu.sphinx.linguist.flat.FlatLinguist.newProperties(FlatLinguist.java:244) at edu.cmu.sphinx.util.props.PropertySheet.getOwner(PropertySheet.java:475) at edu.cmu.sphinx.util.props.PropertySheet.getComponent(PropertySheet.java:285) at edu.cmu.sphinx.decoder.search.SimpleBreadthFirstSearchManager.newProperties(SimpleBreadthFirstSearchManager.java:182) at edu.cmu.sphinx.util.props.PropertySheet.getOwner(PropertySheet.java:475) at edu.cmu.sphinx.util.props.PropertySheet.getComponent(PropertySheet.java:285) at edu.cmu.sphinx.decoder.AbstractDecoder.newProperties(AbstractDecoder.java:65) at edu.cmu.sphinx.decoder.Decoder.newProperties(Decoder.java:37) at edu.cmu.sphinx.util.props.PropertySheet.getOwner(PropertySheet.java:475) at edu.cmu.sphinx.util.props.PropertySheet.getComponent(PropertySheet.java:285) at edu.cmu.sphinx.recognizer.Recognizer.newProperties(Recognizer.java:90) at edu.cmu.sphinx.util.props.PropertySheet.getOwner(PropertySheet.java:475) at edu.cmu.sphinx.util.props.ConfigurationManager.lookup(ConfigurationManager.java:161) at speechRecognition.SpeechRecognition.main(SpeechRecognition.java:16) if you have solution for this problem please tell me .... and thank you .......very nice blog

**[Sagar](#6432 "2011-12-22 18:43:00"):** hello i got that error when i am running this Exception in thread "main" java.lang.NullPointerException at edu.cmu.sphinx.util.props.SaxLoader.load(SaxLoader.java:64) at edu.cmu.sphinx.util.props.ConfigurationManager.loader(ConfigurationManager.java:383) at edu.cmu.sphinx.util.props.ConfigurationManager.(ConfigurationManager.java:115) at com.xebia.HelloWorld.main(HelloWorld.java:27)

**[Sagar](#6433 "2011-12-22 18:44:06"):** hello i m got this error ... Exception in thread "main" java.lang.NullPointerException at edu.cmu.sphinx.util.props.SaxLoader.load(SaxLoader.java:64) at edu.cmu.sphinx.util.props.ConfigurationManager.loader(ConfigurationManager.java:383) at edu.cmu.sphinx.util.props.ConfigurationManager.(ConfigurationManager.java:115) at com.xebia.HelloWorld.main(HelloWorld.java:27) how can i solve it.?

**[sidra](#9235 "2012-07-24 12:19:18"):** plz can u make tutorial for it...?????

**[vaibhav](#8174 "2012-03-31 21:34:27"):** good work can u pls mail me entire project....pls

**[Shantanu](#7909 "2012-03-13 15:04:18"):** hi,i am a b.tech. final year student and i have to submit my project til tuesday.Can u plz help me in making the program which can recognize all the words my email id is shantupandey@gmail.com

**[Jaiz](#7917 "2012-03-14 11:44:21"):** Nice work Shruti Mehra :) Thanks for sharing. I am working for android also. Can we integrate sphinx to android? Thanks

**[sidra](#9173 "2012-07-18 12:29:29"):** Shruti can you plz make a tutorial for this so that we can easily understand.

**[Ravi](#8136 "2012-03-30 13:35:09"):** pls dear tell me ..how can i do speech to text in android and save into a database????? pls help me....... pls pls.... thanx....


---
layout: post
header-img: img/default-blog-pic.jpg
author: pranjan
description: 
post_id: 8253
created: 2011/04/06 01:03:20
created_gmt: 2011/04/05 20:03:20
comment_status: open
---

# Junit Tests - The Myths Around Coverage

Nowadays when Agile is on its peak and Junit tests hijack the technical discussions. Its very easy to find people obsessed with Junits. You can really sense the excitement in people when they talk about Junits.

I consider this as one of the major achievements of the Agile promoters. They have been really successful in transfusing Junits in the blood of developers, managers and even clients. Generally, I see questions in demo regarding the code coverage. And then, I see the team writing Junits, one after the other, to set the Junit coverage above the expectations of the client.

However, most of the people, who are into this highly geeky practice of writing Junit tests, really write Junits which are even worth a penny?

If we do a voting for the reasons of writing Junits, and developers are allowed to vote without their manager or client watching them, I am pretty sure that “Increasing Code Coverage” is what will emerge as the clear winner. And I do not blame developers for it.

The manager or the client, who pushed them to write Junits is the real culprit. Regarding the manager, I will say, that he rarely sees the code, or never sees it. But, he wants a good coverage to show to the client. Its business.

All, the client generally knows about the code is the statistics generated by various tools. So, the only thing he bothers about, is the code coverage. And I do not blame the clients for this expectation. Because this is what the Agile coach told them. This is what he knows. “All the code should be covered with Junit tests”. And then you will gain eternity. Huh.

Just a thought, if everyone will keep talking about the code coverage and Junit tests, then who will talk about the code. Basically, the focus has shifted to write Junit tests which tests the code, rather than the actual code.

The developer keeps on writing code, ifs, elses, switches, methods after methods. The class size grows upto 1000-2000 lines long. But, the code is covered with Junit tests. And when you will see the Junit tests, it will scare you.

The Junit tests for large classes are 100-200 lines long. Whenever someone will write some code, he will add another two lines in Junit test also, So that the test can pass and the code executes. No matter what the Junit test is testing. Sometimes, Junit tests become so hard to modify, that people comment out the assertion, so that the test can pass and they can commit the code into repository. Will we get any benefit from this type of Junit tests or coverage which these Junits show? NO.

We need Junit tests to make sure the code is doing what we expect it to do. But if the Junit itself has become too complex, how can we use it for any such purpose. Its no more simple and understandable.

Moreover, these complex and long Junit tests sometimes become bottleneck in development. You changed something and you need to fix the complex Junit test covering it. The time taken in making Junit tests pass is sometimes much more than what would have been taken to write a Junit test from scratch. And since you or your peer has spent days writing the Junit, you do not want to delete it. Because its so close to your heart. You invested so much time in writing it. Bullshit.

My suggestion is, the time you see a Junit test which you can not understand, delete it and rewrite it. In most of these cases, the class, the Junit test was covering would need refactoring. See whether the class is doing too much. If its doing a hell lot of job, break it into simple pieces. And write simple and logical Junits for them. If you have existing Junit test cases, it will help as a safety net while refactoring. The tests will ensure that the refactored code does not breaks the functionality ( if the existing Junit tests were not rotten).

But what if you need to refactor classes which have no existing Junits?? People will say, that, never ever refactor a code which is not covered by Junit tests. Because Junit tests is a safety net around which you refactor. So, does this means that you should write Junit tests first for the complex classes and then refactor?

NO. Simply NO. Until and unless there is nothing else to do in the project or you really want to make your life hell. In short, until you have lots of time.

Writing junits for pre existing complex classes will not be that easy. It will take hell lot of a time. Also, the tests will not make any sense, as they will be too complex to understand and maintain. The Junits lose their sanity after the class being tested breaches a complexity level. And if you have really less time, then try to write Junit tests for classes which get changed really frequently. Junit test around a class you know is never going to change is useless. Always have tests around classes which change frequently.

So, just try to do some initial safe and easy refactoring first. So that you can add some basic junits. And then use these junits to refactor the code mercilessly to its simplest form. The refactored code, broken into simple understandable pieces will have the logical junits which will make sense.

If you have a tool which shows the statistics about the project in timeline, the complexity should decrease gradually, when you refactor, along with increase in the code coverage.

So, shed the obsession with Junit tests. Start writing “Clean Junit Tests”.

## Comments

**[Amit](#5470 "2011-04-15 17:39:39"):** I guess if your method length is 200 line and its calling 10 private methods. then I think your class is a good candidate for re-factoring. Junits also tell us when your code need re-factoring. It shows you the darkness of code. Now its up to us how we handle it.

**[Kamal](#5407 "2011-04-06 21:31:47"):** Hi Paritosh, sonar is an open source tool that can be used to shows the statistics about the project in timeline.

**[Paritosh Ranjan](#5408 "2011-04-06 22:32:27"):** @Kamal - What is the point that you are trying to make? Looks like a definition of Sonar.

**[Kasper Sørensen](#5409 "2011-04-06 23:24:34"):** Hi Paritosh, Thanks for a delightful take on unittesting. I also agree that unittesting is something that has overwhelmed the SD business a bit too much. Don't get me wrong, I love unittesting when it works as intended but sometimes it's also just plain stupid. Sometimes I feel like we're reproducing the production code 1-to-1 in the unittests just to mock an environmental dependency of what we're testing. This kind of test doesn't assert that "the system works" but asserts that "the test matches the production code" and those assertions are useless. Agree that Kamals comment is BS...

**[Paritosh Ranjan](#5501 "2011-04-25 12:26:45"):** Thanks for your comments Shrikant. I agree with your thoughts. I think that Junit Tests and Simple Code are complementary. Complex code is something which is created not only by writing dirty code, but also by applying all design principles, oops concepts and best practices that the developer has read. This all makes the code too rigid and complex to test. As Martin Fowler Quoted "Cleverness and performance are major delusions of programming." Make it simple, make it testable.

**[Paritosh Ranjan](#5415 "2011-04-07 15:27:55"):** Hi Kasper, Thanks for sharing your thoughts. I think, Junit Tests, if written with a mindset of securing the code, can really help a lot. However, thats not the case as mostly its done to increase coverage. I think it will take some time to come out of this mindset. Till then, everyone will keep wasting time :) (and money :))

**[ShriKant Vashishtha](#5500 "2011-04-25 11:27:57"):** I think the reason is - people have started setting the wrong precedents. The default philosophy is to focus on design principles, decoupling and create better design to improve testability. Unfortunately to some people code-coverage becomes the defacto quality standard which otherwise in Dr Venkat's words is just one vital parameter which if goes wrong means you need to take some corrective actions. Body (software) is not healthy if other important vital parameters are not in good shape except code-coverage. So in your case, if you have 200-300 lines of test code, that's at best a smell which further highlights more serious design issues. The thumb rule is to have less number of lines for a method, less number of lines in a class, more methods and more classes.

**[Amit](#5451 "2011-04-11 12:50:11"):** If we will write a junit for each condition. Then we can easily maintain them. If we try to cover multiple condition in a single test method , That is not the best way of writing Junits and they are harder to maintain. So try to write smart and short Junit which focus on the purpose very well. these test will never be hard to understand. Don't write just the sake of cover the code.

**[Paritosh Ranjan](#5452 "2011-04-11 15:40:12"):** Amit, Will you be able to write a simple junit test for a 200 line long method calling 10 private method inside its 1000 line long class? If you say yes, you need enlightenment. If you say no, then that's what I have explained over here. Don't bother about your junit tests. Bother about your code. Strive for a simple code, and simpler junit tests will follow. But if you will invest time in making your junit tests simple, without simplifying the code, Its never gonna work.


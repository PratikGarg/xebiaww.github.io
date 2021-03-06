---
layout: post
header-img: img/default-blog-pic.jpg
author: Rajdeep
description: 
post_id: 9664
created: 2011/09/19 10:28:33
created_gmt: 2011/09/19 05:28:33
comment_status: open
---

# Grand Central Dispatch (GCD) vs NSThread

In this post I will talk about [Grand central dispatch][1], how it is better than using [NSThread][2]. But before that I would like to see how and where threads are used. There are n number of distinct reasons behind using threads based on the structure and complexity of the app, but the prime reason behind using threads is **asynchronously working on a task** and then (if required) notifying the main thread. The whole idea behind doing so is that we do not want to block the main thread while we are executing a task. So lets see how it is implemented. Here is the small code snippet where we use NSThread to start a task in a new thread, do some time consuming task and notify to the main thread when we are done so that it can update the UI (The main thread is/should be used to update the UI).  [sourcecode lang="javascript"] -(void)doSomeTask{ [NSThread detachNewThreadSelector:@selector(handlerForTask) toTarget:self withObject:nil]; }

-(void)handlerForTask{ NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init]; [self performSelectorOnMainThread:@selector(notifyMainThread) withObject:nil waitUntilDone:NO]; [pool release]; } [/sourcecode]

The same implementation can be done using GCD as given below. [sourcecode lang="javascript"] -(void)doSomeTask{ dispatch_queue_t queue = dispatch_queue_create("com.xebia.gcd", NULL); dispatch_queue_t mainQueue = dispatch_get_main_queue(); dispatch_async(queue, ^{ [self processTask]; dispatch_async(mainQueue, ^{ [self notifyMainThread]; }); }); dispatch_release(queue); }[/sourcecode]

See the obvious takeaway from the code above is that it is cleaner and easier to realize. In the threads we have to manage autorelease pool so that all the memory allocated is released properly. Also if we wanted to **fire multiple selectors with multiple arguments**, we could have easily done that using GCD. Here is the code snippet. 

[sourcecode lang="javascript"] -(void)doSomeTask{ NSDictionary *info ; dispatch_queue_t queue = dispatch_queue_create("com.xebia.gcd", NULL); dispatch_async(queue, ^{ // multiple selectors with multiple arguments [self executeFirstTaskWithInfo:info]; [self executeSecondTaskWithInfo:info andTaskNumber:2]; }); dispatch_release(queue); }[/sourcecode]

Apart from these here are the other advantages of using GCD instead of threads. 

  * The developer has to be careful about his/her strategy for using threads i.e. he/she has to manage problems like **lockings, thread reuse** etc. Where as GCD does all this for you.
  * Threads are highly memory consuming, whereas in GCD there are more cpu cycles available for executing tasks.
  * **Dispatch queues** available in GCD helps us to schedule tasks more efficiently in GCD.

To use GCD you just have to include "dispatch/dispatch.h" and it just works. In the next post I will try to analyze **NSOperation and NSOperationQueue**, which has also started using GCD instead of threads. 

   [1]: http://developer.apple.com/library/ios/#documentation/Performance/Reference/GCD_libdispatch_Ref/Reference/reference.html
   [2]: http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSThread_Class/Reference/Reference.html
---
layout: post
header-img: img/default-blog-pic.jpg
author: ramsharma
description: 
post_id: 9938
created: 2011/10/16 13:52:19
created_gmt: 2011/10/16 08:52:19
comment_status: open
---

# Multitasking aka Multi-Processing in PHP

PHP is now quite old in industry and has been used widely in web based applications. PHP has also evolved much more as enterprise web application language. But PHP still lacks a lot many features, which are very commonly and heavily used in Java, .Net or any other enterprise language. One of those lacking features in PHP is 'Multi-tasking' or support for concurrent processing.

To implement multi-tasking in PHP, people have found a couple of ways like by using pcntl_fork(), exec() and curl. You can Google out those solutions easily.

Today, I am going to explain you one more way to implement 'Multi-tasking' using Gearman library and Gearman server. To implement Gearman solution for PHP, you first have to install Gearman server to your machine. You can find more details about Gearman and its installation here: [http://gearman.org/][1]

Gearman server comes in 3 flavours C, Java and Perl.

I am using Gearman C implementation 'gearmand' as Gearman server in this blog. You can download & install gearmand from here <http://gearman.org/index.php?id=getting_started>. After installing it you will have to start the server by running this command:

[sourcecode language="php"] $ /usr/local/sbin gearmand -d

# or

$ gearmand -d [/sourcecode]

If you are using windows or Mac, I would suggest you to use Java version of Gearman server. I have not tried that but I think Java version can easily be installed and run on Windows/Mac.

Now when you have Gearman server in place and running you can install any of the PHP client to work with Gearman server. There are two solutions available officially to use Gearman for PHP: 

  1. 1\. PHP extension (currently only supported on Linux but you can try to compile the library on Windows by using Cygwin)
  2. 2\. Net_Gearman - pure PHP implementation, a pear module
I am using PHP extension on my Ubuntu machine to run examples to explain Multi-tasking. Once you installed extension on your machine (see here for help: http://docs.php.net/manual/en/book.gearman.php), do the following steps:

Create worker php file worker.php or gearman-worker.php. A gearman worker is thread running as worker to perform a specific task requested by clients. Like in this example we are going to use string reverse method as worker task. Here is the code for worker:

[sourcecode language="php"] <?php // following example code is taken from : http://www.php.net/manual/en/gearman.examples-reverse.php echo "Starting\n";

# Create our worker object.

$gmworker= new GearmanWorker();

# Add default server (localhost).

$gmclient->addServer();

# Register function "reverse" with the server. Change the worker function to

# "reverse_fn_fast" for a faster worker with no output.

$gmworker->addFunction("reverse", "reverse_fn");

print "Waiting for job...\n"; while($gmworker->work()) { if ($gmworker->returnCode() != GEARMAN_SUCCESS) { echo "return_code: " . $gmworker->returnCode() . "\n"; break; } }

function reverse_fn($job) { echo "Received job: " . $job->handle() . "\n";

$workload = $job->workload(); $workload_size = $job->workloadSize();

echo "Workload: $workload ($workload_size)\n";

# This status loop is not needed, just showing how it works for ($x= 0; $x < $workload_size; $x++) { echo "Sending status: " . ($x + 1) . "/$workload_size complete\n"; $job->sendStatus($x, $workload_size); sleep(1); }

$result= strrev($workload); echo "Result: $result\n";

# Return what we want to send back to the client. return $result; } [/sourcecode]

The above code is creating a GearmanWorker and attaching it to the default Gearman server (local gearmand server process, you can pass server ip and port if your gearman server running on different machine)

To see if the above worker is running properly and producing expected results, you have to run the worker by using the following command:

[sourcecode language="bash"] $ php gearman-worker.php [/sourcecode]

Now create a client to create Job for the worker. Here is the code for the client.php or gearman-client.php:

[sourcecode language="php"] <?php // following example code is taken from : http://www.php.net/manual/en/gearman.examples-reverse.php

# Create our client object.

$gmclient= new GearmanClient();

# Add default server (localhost).

$gmclient->addServer();

echo "Sending job\n";

# Send reverse job

do { $result = $gmclient->do("reverse", "Hello!");

# Check for various return packets and errors. switch($gmclient->returnCode()) { case GEARMAN_WORK_DATA: echo "Data: $result\n"; break; case GEARMAN_WORK_STATUS: list($numerator, $denominator)= $gmclient->doStatus(); echo "Status: $numerator/$denominator complete\n"; break; case GEARMAN_WORK_FAIL: echo "Failed\n"; exit; case GEARMAN_SUCCESS: break; default: echo "RET: " . $gmclient->returnCode() . "\n"; exit; } } while($gmclient->returnCode() != GEARMAN_SUCCESS);

?> [/sourcecode]

Now when you run the above code by:

[sourcecode language="bash"] $ php gearman-client.php [/sourcecode]

It will generate a JOB for gearman worker(s) available on server (in our case, we created only one worker but you can run multiple workers for the same JOB). The above code is not running the reverse job in background or you can say not running in multi-threaded style. Like we have used:

[sourcecode language="php"] $result = $gmclient->do("reverse", "Hello!"); [/sourcecode]

Which will hold the code execution of the client until it gets the result or response from worker. To make it multi-threaded you have to modify your code a bit. For example see below:

[sourcecode language="php"] <?php //http://docs.php.net/manual/en/gearman.examples-reverse-bg.php

# create our client object

$gmclient= new GearmanClient();

# add the default server (localhost)

$gmclient->addServer();

# run reverse client in the background

$job_handle = $gmclient->doBackground("reverse", "this is a test");

if ($gmclient->returnCode() != GEARMAN_SUCCESS) { echo "bad return code\n"; exit; }

echo "done!\n";

?> [/sourcecode]

Now just by making the call to worker as:

[sourcecode language="php"] $job_handle = $gmclient->doBackground("reverse", "this is a test"); [/sourcecode]

The code execution will not halt. doBackground() method is the method which is being used for creating a separate thread to perform a specific JOB. The above program will do:

[sourcecode language="php"] echo "done!\n"; [/sourcecode]

and finish the execution as soon as the Job is sent to worker (as it has nothing more to do, you can make some more logic and then check the client status and response)

**Conclusion**: Multi-taskingin PHP is not supported in PHP natively, but you have several ways to achieve the same. Gearman server and client libraries is one of the ways you can choose to implement multi-processing in PHP very easily. You can user Gearman library for processing heavy application like: image processing, resizing or do bulk data manipulation, sending emails in bulk etc.

   [1]: http://gearman.org (Gearman)

## Comments

**[János Szurovecz](#6027 "2011-10-17 01:35:06"):** First of all, it's a good article about Gearman. But basically it is not multithreading, it's multi-processing. Of cource in some cases it is better because you can use more than one machine, but processes cannot share resources for each other, threads can. The other thing is most of available PHP libraries doesn't handle long-running processes. For example it's not enough to open a DB connection, you have to check later its availability.

**[Ram Sharma](#6029 "2011-10-17 20:41:55"):** Thanks Janos for your feedback :) . I will definitely check the DB problems.


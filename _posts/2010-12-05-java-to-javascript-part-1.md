---
layout: post
header-img: img/default-blog-pic.jpg
author: rrawat
description: 
post_id: 6451
created: 2010/12/05 20:41:10
created_gmt: 2010/12/05 15:41:10
comment_status: open
---

# Java to Javascript - Part 1

A few years(before Web2.0) back a Java developer moving to Javascript would have missed a lot of things from his/her extreme programming bible (Unit tests, Mocks, OO (not really)). In this blog i try to cover my experience of moving to Javascript development and a few new frameworks that bring good practices to Javascript development. Almost everything is based out of jQuery world as that is what i am using, other frameworks like Prototype/YUI must be great but i have not worked on them.

For using jQuery i would say 

“You add $ to anything and it becomes possible.. literally”.

**Logging and Debugging**

First things first. Developer consoles in IE 8/ Chrome/ Firefox are great, you can log stuff to these consoles.Firebug console API is here so you could do a lot of things in firebug. We identified one performance related issue using by simply using the time function (Firebug Profiler’s output was too fine grained and did not point to exact code region). I do not use the debugger as our scripts are loaded dynamically so they do not help anyway. Sufficient logging always helps. If you really want to persist your logs, you could asynchronously post the logs to server.

[code language="Javascript"]

(function($){ if(console) { $.log = function(obj) { console.log(obj); } } })(jQuery); 
 ```

You can look at the [Firebug console API][1] for other methods available on the console.

Super cool alert: You wrap alert(JSON.stringify(obj)) as $.alert and see the object as inside out when you want (you will need json2.js).

You can also have a look at http://plugins.jquery.com/project/jQueryLog (which has nice templating feature as well).

**Dependency Injection**

Being a dynamic language this has never been a problem for Javascript as you could add /remove properties at will to an object or all of it’s _types_. Following code shows a small IOC container implementation. The DI takes as config input and wires up the objects.  The _getClosure method on line #12 is a fix for strange problem i faced with the closures. It kind of showed me why anonymous inner classes would only access the final variables of the scope outside. I was using an inline function definition on line #12 and binding it to the dependency name, but it was always binding to the last value of the variable.

[code language="Javascript"] com.framework.DIEngine = { _objCache: {},

init: function(config) { for (obj in config) { var instance = config[obj].instance; if (config[obj].dependencies) { for (depIdx in config[obj].dependencies) { var dep = config[obj].dependencies[depIdx]; $.log(dep); $.log('bound to:' + dep); instance[dep] = this._getClosure(dep); // bind a function to ask from DIEngine } } this._objCache[obj] = instance; // store the instance } },

_getClosure: function(dep) { return function() {return com.framework.DIEngine.get(dep)}; },

get: function(name) { $.log('Asked DI for:' + name); return this._objCache[name]; } };


 ```

**Packaging**

You can mirror packages by creating objects inside a hierarchy. Your declarations automatically add new classes/objects to the namespace. By doing this you can rest assured that you are not conflicting with any other variables in some other library.

A sample namespace can be declared as below and then you define all your objects in the attached namespace.

[code language="Javascript"] var com = { framework:{}, app:{} };

// define objects in namespace. com.app.Mapper = { // foo bar } 
 ```

**Object Orientation**

One of the major things that a java developer misses is the absense of class hierarchies, it is hard to model the data without significant code duplication. Following articles describe how to make use of javascript’s object oriented nature. A simple approach is to maintain a registry of objects and then create new objects by extending registered objects. We had objects which would have a single instance( singletons) so it was easy. John Resig’s link below explains in detail how to implement object orientation. Code looks much structured and readable compared to prototype based approach mentioned in sitepoint article. 

  * <http://articles.sitepoint.com/article/oriented-programming-1>
  * <http://www.javascriptkit.com/javatutors/oopjs.shtml>
  * <http://ejohn.org/blog/simple-javascript-inheritance/>
[code language="Javascript"] var OO = { _ooRegistry: {},

register : function(subName, base, subDef) { if (!(typeof base == 'string') ) { subDef = base; // will this become global? base = {}; } else { base = this._ooRegistry[base]; } this._ooRegistry[subName] = $.extend({},base, subDef ); this._ooRegistry[subName]._super = base; }, get: function(cls) { return this._ooRegistry[cls]; } };

OO.register('baseClass', { hello: function() { $.log("Base class invoked."); return "Namaskaar"; } });

OO.register('subClass','baseClass', { hello: function() { $.log("Sub class invoked."); $.log("Invoking super"); this._super.hello(); return "Wazz up."; } });


 ```

**Unit Testing**

Let's now put the code above to test.  jQuery offers [QUnit][2] as the unit testing framework. Following code puts the DI code and the OO code to test. QUnit provides nice setup and teardown lifecycle methods as JUnit.

[code language="Javascript"] // OO test $(document).ready(function() {

module("OO tests.");

test("OO get test", function() { var base = OO.get('baseClass').hello(); var sub = OO.get('subClass').hello(); ok(base == 'Namaskaar', 'Base class object invoked.'); ok(sub == 'Wazz up.', 'Sub class object invoked.'); }); });

// DI test $(document).ready(function() {

module("DI tests.", { setup: function() { com.framework.DIEngine.init({ reducer: {instance:com.app.Reducer}, mapper:{instance:com.app.Mapper}, service:{instance:com.app.Service, dependencies: ['reducer', 'mapper']} }); }, teardown: function() { // tear down.. } });

test("DI get test", function() { expect(2); // 2 assertions in the test var mapper = com.framework.DIEngine.get('mapper'); ok(mapper && typeof mapper == 'object', 'Mapper is defined & is an object.'); ok(mapper.name == 'mapper', 'It is indeed mapper.'); }); }); 
 ```

QUnit can be integrated with the continuous integration systems which launch browsers and the collect test results from the DOM. QUnit documentation explains how this can be done. QUnit provides nice callbacks for various events in the test lifecycle. I find QUnit.done of particular interest as it can be used to inform you of test failures by posting back to a URL ( an app there could trigger a message to your team's instant messaging system).  Other callbacks can be used to collect the data on individual tests and then send it on 'done' callback.

In the next part of this blog i will try to cover 

  * Use of mocks in Javascript testing
  * Dependency management
  * Messaging ( events!!)
  * The _this_ mystery
  * Deployment and code checks ( JSLint)
**Even More**

   [1]: http://getfirebug.com/wiki/index.php/Console_API
   [2]: http://docs.jquery.com/Qunit (QUnit)
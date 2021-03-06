---
layout: post
header-img: img/default-blog-pic.jpg
author: sunil.inteti
description: 
post_id: 862
created: 2009/01/02 09:46:14
created_gmt: 2009/01/02 08:46:14
comment_status: open
---

# Why and When to use Groovy

I have just started learning Groovy few days back.The first thing that I have asked myself and searched for is 'Why should i use groovy over java and When I can use Groovy. ?'

I am following the book 'Programming Groovy'. The things I have understood regarding 'Why' part are  **Simplicity **: Groovy syntax is simple and easy. It saves a lot of code and effort thus increasing the productivity of developer if he had to do the same thing in Java.

**in Java **

`for(int i = 0; i < 3; i++) { System.out.println("United United..." ) }`

**in Groovy **

`3.times { println "United United..." }`

This is just a trivial example but there are lot more things like this.

**Groovy's Dynamicity **: This is one reason why coding is made simpler and concepts like polymorphism and callback patterns are made easy to implement.

`class Player { String name int age } class Club { String name String city } public printNameOfTheFootballingEntity(entity) { println "the entity with name "+ entity.name + " is related to Football" }`

`player = new Player(name:"Giggs", age:35) club = new Club(name:"ManchesterUnited", city:"Old Trafford") printNameOfTheFootballingEntity(player) printNameOfTheFootballingEntity(club) `

Consider above code. It prints the following:

_the entity with name Giggs is related to Football the entity with name ManchesterUnited is related to Football_

If you observe there is no compile time check for the presence of the property "name" in the entity. While interpreting Groovy checks for this property and if it is not there it throws the Missing Property Exception. Dynamic!! ain't it?

In Groovy you dont need to end statement with semicolon.

Closures are the piece of code blocks contains some statements. They are like fucntion pointers in C. They are infact objects in groovy and can be passed to functions as arguemts as well. Closures can do the job of callbacks.

For Example

`def checkPlayer = { println it + " is a good Striker" } ` `list = ["Rooney", "Tevez" , "Berbatov" ] list.each(checkPlayer)`

The above code prints the following

_Rooney is a good Striker Tevez is a good Striker Berbatov is a good Striker_

Here **'it'** represents the each single item in the list that is passed to the Closure 'checkPlayer'.

The code inside the braces is closure code acting like a callback for all the elements of the set.

The same thing in Java will be a bit more effort to code.
    
    
        <code>List<string> list = new ArrayList<string>();
        list.add("Rooney");
        list.add("Tevez");
        list.add("Berbatov");
    

` `for (String player : list) { checkPlayer(player); } private void checkPlayer(String player) { System.out.println(player + "is a good Striker"); }`

The examples so far shows the ease with which things can be developed with little effort but ultimately have the same result because the groovy code is compiled to bytecode which JVM understands. This can make a developer more productive and it can give more agility.

Groovy accepts almost all Java programs as it can use the Java libraries and it even extends some core Java classes as well.

For example

`list.join() `

This prints 'RooneyTevezBerbatov'. Groovy has added the join() method on the 'List' of Java while retaining remaining methods.

All is well. These are some reasons which are good enough for any Java developer to explore Groovy.

Now the big Question arises 'When can I use Groovy over Java?' and 'What sort of applications are targeted?

I am at the moment stuck at this question. Honestly speaking i didn't get a sufficient answer . I think this 'When' question is really important especially if some thing is improved over some other thing and you are comparing both.

Groovy scripts are interpreted at runtime, this might cause some performance overhead, which is not all that bad. You can always compile it to java bytecode to remove that performance penalty. Other reasons may be revealed to myself after some more exposure, about what kind of applications can be created using Groovy. I will share it my next blog. But any suggestions and thoughts are always welcome!

**References:** 'Programming Groovy' Book
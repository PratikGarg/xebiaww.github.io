---
layout: post
header-img: img/default-blog-pic.jpg
author: ssethi
description: 
post_id: 16634
created: 2013/06/19 09:54:51
created_gmt: 2013/06/19 04:54:51
comment_status: open
---

# Escape from Jar Hell

Let's say you are visiting a bank for opening an account and you forgot a document which is must for opening your account. You quickly drive back home to get the document and you find two exactly similar files on your table. Now, the document you are looking for is there in one of these files but because the files are exactly similar in appearance, you cannot precisely decide which is the file you are looking for. You will browse through both the files [in case you didn't find the document in first file] , get the document and rush back to bank and have your account opened [Congrats ]. Now, if there would have been a hypothetical restriction that you can only look into one of the files available on the table, you might have not got the required document ever. If you have ever been into situation like this........ then my friend.... you have faced the pain of JAR Hell. 

## 

## What is Jar Hell?

Many times it happens that your java functionality is working perfectly with a "Jar A" on classpath until you realize that the new functionality you are working on, is not supported by the version of "Jar A" being used. You can not just replace the "Jar A" as there are some changes in newer version of "Jar A" which might impact existing functionality. So you decide to keep both versions of "Jar A"  on application classpath. After building the new functionality when you ran your junits, you realized that existing test cases are failing though you still have the older version of "Jar A" on your classpath.

Whoa, what happened here... it should have worked!!! With that reaction you started debugging it and found that your junit is referencing com.jar.hell.Problem class which is also available in new "Jar A" with different method syntax or functionality and the introduction of new "Jar A" have superseded the existing "Jar A". So your junits are referencing com.jar.hell.Problem from new "Jar A" whereas you wanted that exising functionality should continue to access same class from existing "Jar A". This problem typically is called as JAR Hell. Java System by default loads the first class available on classpath and all other classes the will get a reference of this class wherever required. Hence, in the example above, though we wanted to use older version of class for existing functionality but since newer version was loaded first, the existing junits failed. 

## Solution

If you are in a Jar hell and you are looking for a solution... then next line will resolve your problem.

**HAVE ONLY ONE VERSION OF JAR ON YOUR CLASSPATH.**

Well Well Well, this is not solution!!! I know, you already know this but believe me ideally you should not have multiple versions of Jar in same application. You should upgrade your system completely as per new jar. But that's an ideal scenario and the world is not that ideal. Upgrade usually requires lot of time and money which may not be desired specially when you are running against the deadlines.

Having said that, we do have a solution for Jar Hell and that is what this post is all about. Last time I faced this problem, I googled for several hours, found many of code snippets but none of them worked in first go [:(]. Once I got it working (yes it worked), I retrospected my code and realized that I was always close to solution but since I was not clear with the concept and the answers I got from web were not direct but just indicative. So I decided to put together an example to explain the "Escape from Jar Hell". Let's discuss it in detail. 

### Problem Statement

Most of software progammers love coffee machines. I remember one of my colleague once said that "it is treat to have coffee in office" and honestly I echo the same. Similarly there was a coffee machine in one of the IT firms which used to prepare coffee using powedered milk.  The coffee machine in Java looked like following interface:

[java] package com.myorg.pantry; public interface CoffeeMachine { public String prepareCoffee(String coffeeType); }[/java]

which is implemented as following

[java] package com.myorg.pantry; import com.myorg.pantry.rawmaterial.CoffeeIngredientSupplier; public class CoffeeMachineImpl implements CoffeeMachine { @Override public String prepareCoffee(String coffeeType) { String coffee = null; if (coffeeType.equalsIgnoreCase("powdered-milk-coffee")) { CoffeeIngredientSupplier ingSupplier = new CoffeeIngredientSupplier(); String milk = ingSupplier.getMilk(); ingSupplier.addCoffee(); ingSupplier.addSugar(); coffee = milk + " Coffee"; } return coffee; } } [/java]

As we can see CoffeeMachineImpl uses class CoffeeIngredientSupplier which is available in ingredient-powdered-milk.jar.  CoffeeIngredientSupplier provides ingredients for preparing coffee to Coffee machine. Following is it's implementation

[java] package com.myorg.pantry.rawmaterial; public class CoffeeIngredientSupplier { boolean isMilkAvailable = false; /*_ * Loads powdered milk and returns it to calling machine @return _/ public String getMilk() { if (isMilkAvailable) { loadPowderedMilk(); } return "Powdered Milk"; } private void loadPowderedMilk() { System.out.println("Powdered Milk Loaded"); } public void addSugar() { System.out.println("Sugar Added..."); } public void addCoffee() { System.out.println("Coffee Added..."); } } [/java]

Some employees with special taste buds (like me) approached managment and requested that they don't like powdered milk coffee and they are looking for coffee with bottled milk. Management was happy that employees didn't ask for increments  , bottled milk coffee is no big deal, so they ordered coffee machine company a new extension for bottled milk coffee. Coffee machine company provided an extension for existing machine which can be used for bottled milk coffee now. The extension was in the form of ingredient-bottled-milk.jar. A system engineer visited the company to install the new extension with existing machine. Mr. Engineer, smartly enough, added the new jar on classpath and integrated the new extension as following:

[java] package com.myorg.pantry; import com.myorg.pantry.rawmaterial.CoffeeIngredientSupplier; public class CoffeeMachineImpl implements CoffeeMachine { @Override public String prepareCoffee(String coffeeType) { String coffee = null; if (coffeeType.equalsIgnoreCase("powdered-milk-coffee")) { CoffeeIngredientSupplier ingSupplier = new CoffeeIngredientSupplier(); String milk = ingSupplier.getMilk(); ingSupplier.addCoffee(); ingSupplier.addSugar(); coffee = milk + " Coffee"; } else if (coffeeType.equalsIgnoreCase("bottled-milk-coffee")) { CoffeeIngredientSupplier ingSupplier = new CoffeeIngredientSupplier(); String milk = ingSupplier.getMilk(); ingSupplier.addCoffee(); ingSupplier.addSugar(); coffee = milk + " Coffee"; } return coffee; } } [/java] 

Great...all set now!!! Somebody from admin team informed all employees about the latest coffee machine and some excited employees rushed to cafeteria to taste bottled milk coffee. After sorting out that who will inagorate the new coffee button, an employee got the new coffee in his mug. The moment he tasted the coffee...... the reaction was.. "Yaakkk.. it's the same old coffee".

## Comments

**[Mohit Kanwar](#9485 "2014-05-23 16:04:14"):** Nice!


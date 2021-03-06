---
layout: post
header-img: img/default-blog-pic.jpg
author: sarora
description: 
post_id: 3424
created: 2010/05/03 21:22:59
created_gmt: 2010/05/03 16:22:59
comment_status: open
---

# Implementing Command Pattern using Spring injection

Recently I was working on a user story and after the initial design and a bit of implementation, I noticed that the code is not organised nicely and also making my life hard from the unit testing standpoint. The business problem my code addressed was building a rule engine (built with a set of validation rules)  that is applied on the input from the client , generate warning, error messages and validating the input.So, this is how I designed my implementation at first

[sourcecode language="java"] class Validator { ValidationResult result;

public ValidationResult validate(input){ rule1(); rule2(); rule3();

* * *

* * *

}

private rule1(){ doSomething... } private rule2(){ doSomething... } ...and so on. } [/sourcecode]

At first when I read the user story documentation, I had to apply 4 rules in all, but think of a scenario when the user has a couple of more rules to be applied on the input now and wants to take out a few which we implemented in the first place , what would u do now?

Add 2 more private methods named rule5 and rule6 to Validator and add a call to both in the validate method , also take out method rule1(since that rule has become obsolete now) Now, because I had only one method exposed from Validator,the unit test case(called ValidatorTest) also broke and I had to capture the scenarios covered by the two new rules added and remove the obsolete one.

Looks clumsy , doesn't it?, I thought of a better design approach from the clean code and unit testing perspective and came up with the following design

Validator now would only have one method called validate and no private methods inside it , since all the rules take input as a parameter and return validation result as the output, created the following interface

[sourcecode language="java"] interface ValidationRule { ValidationResult validate(input,result); } [/sourcecode]

I pulled out all the private methods from Validator and created a class for each one of those with the following implementation

Note : result is an instance of ValidationResult only

[sourcecode language="java"] class Rule1 implements ValidationRule{ public void validate(input,result){ [business logic here .....]; } }

class Rule2 implements ValidationRule{ public void validate(input,result){ [business logic here .....]; } } [/sourcecode]

and so on ...

Lets plugin these into Validator ..I used spring's dependency injection in following way My Validator class would look like

[sourcecode language="java"] class Validator { ValidationResult result; List<ValidationRule> rules;
    
    
    public void setRules(List&lt;ValidationRule&gt; rules){
     this.rules=rules;
    }
    public ValidationResult validate(input){
       for(ValidationRule rule:rules){
         rule.validate(input,this.result);
    }
    

} } [/sourcecode]

In the application-context.xml I added following

[sourcecode language="xml"] <bean id="validator"> <property name="rules"> <list> <bean class="com.xebia.business.rules.Rule1"/> <bean class="com.xebia.business.rules.Rule2"/> <bean class="com.xebia.business.rules.Rule3"/> \----- \----- </list> </property> </bean> [/sourcecode]

I added unit test cases for all the rules as Rule1Test, Rule2Test e.g., independent of one another.

Benefits that I see with this design are following

  1. Cleaner code , Validator is now acting as a proxy for the rules engine and doesn't contain any business logic.

  2. Enhanced testability , all the rule classes can be tested independent of each other.

  3. In future if a rule becomes obsolete , I'll just remove that from the <list> tag in the context file and similarly if new rules need to be added, just add a rule class without touching either Validator and any of the Rule classes and put an entry in the list tag in the context file.

## Comments

**[PSG](#6022 "2011-10-14 00:49:06"):** Thanks for sharing your idea...for long number of days I was looking for this type of clean code example with a real life problem..... Thanks ......


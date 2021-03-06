---
layout: post
header-img: img/default-blog-pic.jpg
author: nkhattar
description: 
post_id: 10527
created: 2011/12/19 16:31:55
created_gmt: 2011/12/19 11:31:55
comment_status: open
---

# Grails Custom TextField Tag: Dynamically adding Domain level Field Constraints

Recently in my project, I came across a requirement to add a custom grails textfield in the application. The application is primarily a Web based application with multiple domain classes having numerous nested level associations with each other. Corresponding to the domain classes, there are multiple view pages like Create, Edit, Show, Search, etc.

The requirement was to add field constraints like ‘**maxSize**’ to both domain classes as well as their respective view pages. Adding such constraints at the domain level is quite easier via ‘[static constraints block][1]’ within each domain class, but reflecting the same on the view page isn’t easy enough.

For e.g. Adding **maxsize **constraint for a field in a domain class is equivalent to adding **maxlength **property in a textbox corresponding to that field on the view page. So, if the maxSize of a field ‘Short Name’ is say 5, then similarly we can add maxlength equal to 5 in the textfield corresponding to ‘Short Name’. _The solution sounds simple & straightforward, but it is neither scalable nor maintainable_.  Adding maxlength property to ‘Short Name’ textfield in every view page, where ‘Short Name’ is used, is a time-taking task and also manually updating the maxlength property in every textfield (on change of maxSize constraint at the domain level) is not at all efficient.

So, the question is How to fulfill this requirement, while taking into account the **scalability **& **maintainability **of the solution. 

One definite solution is to _create a custom grails textfield which in some way reads the ‘maxSize’ constraint value from the domain class and populates the ‘maxLength’ property of the textfield_. In this manner, one need not to manually add or update the maxLength value on every view page, rather one just need to add or update maxSize constraint value at the domain level.

So the next thing to identify was How to create such a Smart Tag? Then, I came across this link "[MaxLength HTML attribute for domain objects][2]" hosted on the official [Grails Smart Tag webpage][3]. The solution is exactly what we discussed in the above lines.

[sourcecode lang="javascript"] def maxlength = object.constraints."${attrs.name}".getMaxSize() if(maxlength) { attrs.put('maxlength', maxlength) } attrs.put('value', fieldValue(bean:object,field:attrs.name)) ... [/sourcecode]

In the above mentioned code, the key thing to look for is

“**object.constraints."${attrs.name}".getMaxSize()**”

**'constraints.field’ snippet in the above line returns an object of type ‘**[ConstrainedProperty][4]**’ which in turn has a list of all applied constraints for that field. ConstrainedProperty Class has a vast api exposed for almost every type of [Constraint][5] like _maxSize_, _blank_, _minSize_, _email_, etc.

But, the moment I tried implementing the same solution in my case, it just didn’t worked. The reason is that the solution is not compliant to **nested level domain class associations**. What I mean by Nested level associations is that suppose Class A has a field ‘b’ which is of type Class B, which in turn has a field ‘c’ of type String. Then referring to the above mentioned solution, the maxlength value of the textfield pertaining to field ‘b.c’ of Class A will be as follows: 

** “a.constraints.b.c.getMaxSize()”**

Hey, but this will not work, because maxSize constraint for ‘c’ is defined within Class B & not in Class A. The above statement should be something like this: 

** “a.b.constraints.c.getMaxSize()”**

Again, for the above statement to execute, ‘b’ must be initialized in order to access its constraints, but in a view page like Create for Entity A, you cannot make sure to initialize ‘b’. Therefore, the above solution is not foolproof. 

**What should be done in such a case? What could be a generic solution for this problem?**

An important thing to notice here is that ‘constraint block/closure in domain classes’ is a static block, so one need not to worry about whether the instance is initialized or not. The thing that one should worry about is how to determine the Class from the field name. The simplest way to determine Class from field name is using **_[java.lang.Class][6]. _**‘Class.java’ has multiple getter methods out of which following are of great help:

  * Field[] **getFields**() - Returns an array containing **[java.lang.reflect.][7]**`**[Field][7]**` objects reflecting all the accessible public fields of the class or interface represented by this `Class` object.
  * Field **getField**(String name) : Returns a `Field` object that reflects the specified public member field of the class or interface represented by this `Class` object.
  * Field **getDeclaredField**(String name) : Returns a `Field` object that reflects the specified declared field of the class or interface represented by this `Class` object.

Using **getDeclaredField(String name)** function, our objective can be easily achieved. ‘_getDeclaredField(String name)_’ function returns a Field Object for the selected field, which in turn has a property ‘type’ that returns you the Class of the selected field. From the returned Class you can then access the static constraints block. That's all and you are done!!!

**Let’s implement the above solution using a real world example:**

**"**There is a _Car _domain class which has two fields - String companyName & CarDetails cardDetails. _CarDetails _class in turn has a field – String color. ‘companyName’ field has a _maxSize _constraint of 20 characters & ‘color’ has a maxSize constraint of 10. You need to design a ‘_create_’ view page in order to allow user to add new car entries into the database. The webpage create.gsp will have two textfields: Company Name & Car Color with respective _maxLength constraints_**"**

Solution:-

**Domain Classes** -

[sourcecode lang="java"] class Car { CarDetails carDetails String companyName static constraints = { companyName maxSize: 20 } }

class CarDetails { String color static constraints = { color maxSize: 10 } } [/sourcecode]

**Custom TagLib** -

[sourcecode lang="java"] class CustomTagLib { static namespace = 'custom' def textField = {attrs -> attrs.type = "text" attrs.tagName = "textField"
    
    
        def bean = attrs.bean
        String field = attrs.name
        if (bean) {
            Class parentBeanClass = bean.class;
            String fieldName = field
            if (field.indexOf('.')) {
    

   [1]: http://www.grails.org/doc/1.3.7/guide/7.%20Validation.html#7.2 Validating Constraints
   [2]: http://www.javathinking.com/2008/03/maxlength-html-attribute-for-domain-objects/
   [3]: http://www.grails.org/Smarter+grails+tags
   [4]: http://grails.org/doc/1.3.x/api/org/codehaus/groovy/grails/validation/ConstrainedProperty.html
   [5]: http://grails.org/doc/1.3.x/api/org/codehaus/groovy/grails/validation/Constraint.html
   [6]: http://docs.oracle.com/javase/6/docs/api/java/lang/Class.html
   [7]: http://docs.oracle.com/javase/6/docs/api/java/lang/reflect/Field.html

## Comments

**[Sunil Prakash Inteti](#6418 "2011-12-19 18:19:37"):** I recommend not to use 'def' If you ready know the type of object in a Grails project. This would improve the performance a bit as there is no runtime look up for the class type for every property/method invocation. Its a good practice in Groovy/Grails projects. BTW, Nice demonstration to show the dynamism of grails and code reuse using the tag libraries.

**[Nitin Khattar](#6422 "2011-12-20 08:00:31"):** @Sunil Thanks a lot for your useful suggestion. Yes, you are right 'runtime lookup' feature of grails should be used to a limited extent till it boosts the flexibility of the code. Will definitely keep it in mind in future...


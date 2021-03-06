---
layout: post
header-img: img/default-blog-pic.jpg
author: nkhattar
description: 
post_id: 14680
created: 2012/07/18 10:22:57
created_gmt: 2012/07/18 05:22:57
comment_status: open
---

# Grails: Extending default 'Errors' functionality

Recently in my Grails project, we came across a requirement where we need to** inject a Warning type of Error** in errors collection of every domain class. The application is primarily a Web based application with multiple domain classes having view pages like Create, Edit, Show, Search, etc. The domain objects generated from the view are passed through a set of validations and if successfully passed, domain object is saved into the database. In case any of the validation fails, then the corresponding errors are saved into database for further reporting.

Now, the requirement was to handle a different type of Validation, named **Soft Validation** which results in adding a Warning or Soft Error against the domain object but never fails the execution workflow. There could have been two ways to handle the same:- 

  1. To create a separate Warnings Collections for keeping track of all the warnings and injecting the same in every Domain Class using Grails [Custom Plug-in][1].
  2. To make use of the existing Error Handling mechanisms provided by Grails Framework, by default in every domain class and extend it to handle the warnings or Soft Errors.
**Option 1** is a much cleaner way without any dependencies involved, but the major problem is that one needs to **reinvent the wheel**. We need to do the same handling for warnings as already done by Grails Framework for Errors Object, while validating or saving the domain object.

In that case it is better **to extend the existing Error Handling mechanism** present in every Grails Domain Class. 

Before going further on implementing Option 2, Lets understand the **importance of injecting Errors Object** in every Grails Domain Class and how that is done.

“The **_errors_** property on domain classes is an instance of the Spring **[Errors][2] **interface. It stores and exposes information about data-binding and validation errors for a specific object. The Errors interface provides multiple methods to navigate the validation errors and also retrieve the original values. The errors property is used by Grails during [Data Binding][3] to store type conversion errors and during [Validation][4] when calling the ‘[validate][5]’ or ‘[save][6]’ methods. One can also add one’s own errors using the ‘_reject_’ and ‘_rejectValue_’ methods.”

The Errors Interface provide various useful API methods for insertion & retrieval of Validation Errors, like _addAllErrors, reject, rejectValue, getAllErrors, getErrorCount, hasErrors, etc. _Spring framework provides separate abstractions for handling Field Level or Global Level_ _errors with corresponding api methods for the same.

In **Grails pre-2.0 **versions, ‘errors’ property is injected in every domain class at runtime using Groovy **[Runtime Metaprogramming][7]**, whereas in **Grails post-2.0** versions Groovy **[Compile-time Metaprogramming][8]** via AST Transformation is used to inject ‘errors’ property. [ASTValidationErrorsHelper][9] class is the AST Transformation class used for the same.

Now, let’s _implement the above mentioned solution_ where we will extend the existing Error Handling Mechanism to handle and manipulate Soft Errors. There are mainly 3 steps required for this:

Step 1 – **Add field “Error Type” in the Error Class**

As mentioned earlier, Spring Framework provides separate abstraction for handling Global level errors as [ObjectError][10] which encapsulates an object error, that is, a global reason for rejecting an object. Field level errors are handled via [FieldError][11] Class which inherits from ObjectError along with properties encapsulating field level information.

What we need to do is to inject a field named ‘**errorType**’ in ObjectError Class or individually in FieldError in order to **differentiate between Error & Warning**. Since these classes are pre-compiled and shipped in Spring framework plug-in, one cannot get access to the source code of these classes. Therefore, in order to add a custom field in these pre-compiled classes, we can make use of **Groovy Metaclass Injection mechanism**. Metaclass Injection provides a way to add new fields, methods, constructors or modify the body of existing methods in a class. Complete information around Groovy Metaclass Injection can be found **[here][12]**.

[sourcecode lang="groovy"] ObjectError.metaClass.errorType = ErrorType.HARD_ERROR [/sourcecode]

*****ErrorType is a user-defined enum having two values - HARD_ERROR, SOFT_ERROR

Here I have injected errorType field into ObjectError Class whose default value is HARD_ERROR. Adding a field in the above manner exposes a default getter-setter method at the runtime, so one need not to expose any getter-setter method explicitly.

Step 2 – **Adding a new method in the Class implementing Spring Errors Interface which accepts an additional argument for errorType**

The most important prerequisite of this step is to_ identify the **implementation class of Errors Interface** in Grails pre 2.0 & post 2.0_ versions. Spring framework provides [BeanPropertyBindingResult][13] Class as the implementation class of Errors Interface which is referenced directly in Grails pre 2.0 versions, whereas in post 2.0 versions, Grails provide its own class [ValidationErrors][14] extending BeanPropertyBindingResult Class, which provides two methods (_getAt, putAt_) for easy retrieval and addition of field errors.

Once this is done, next task is to **inject a new method in the implementation class** that adds an error with supplied ‘errorType’. The signature of new overloaded method will be very much similar to that of existing methods for adding error, with only difference that it will accept an **additional argument of type _ErrorType_**.

[sourcecode lang="groovy"] BeanPropertyBindingResult.metaClass.rejectValue = { String field, String code, Object[] args, String defaultMsg, ErrorType errorType-> String[] codes = ``` 
 FieldError fieldError = new FieldError(delegate.objectName,field, null,false,codes,args,defaultMsg) fieldError.errorType = errorType delegate.addError(fieldError) } [/sourcecode]

*For injecting this new overloaded method, we have again used Grails Metaclass Injection mechanism.

   [1]: http://www.ibm.com/developerworks/java/library/j-grails09159/index.html
   [2]: http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/validation/Errors.html
   [3]: http://grails.org/doc/latest/guide/single.html#dataBinding
   [4]: http://grails.org/doc/latest/guide/single.html#validation
   [5]: http://grails.org/doc/latest/ref/Domain%20Classes/validate.html
   [6]: http://grails.org/doc/latest/ref/Domain%20Classes/save.html
   [7]: http://groovy.codehaus.org/Dynamic+Groovy
   [8]: http://groovy.codehaus.org/Compile-time+Metaprogramming+-+AST+Transformations
   [9]: http://grails.org/doc/2.0.4/api/org/codehaus/groovy/grails/compiler/injection/ASTValidationErrorsHelper.html
   [10]: http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/validation/ObjectError.html
   [11]: http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/validation/FieldError.html
   [12]: http://groovy.codehaus.org/JN3525-MetaClasses
   [13]: http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/validation/BeanPropertyBindingResult.html
   [14]: http://grails.org/doc/latest/api/grails/validation/ValidationErrors.html
---
layout: post
header-img: img/default-blog-pic.jpg
author: vgupta
description: 
post_id: 2031
created: 2009/11/25 13:28:58
created_gmt: 2009/11/25 08:28:58
comment_status: open
---

# Organizing Domain Logic

**Introduction**

Most enterprise applications use Layering as the primary technique to break the complexity of the software projects. Layering involves breaking down the applications in various layers where each layer lie above the other and provide services to the higher layer. Layering provides following benefits 

  * You can understand about a layer without knowing about other layers. For example, if you know how [TCP][1] works you can create your own [FTP][2] service without actually knowing about the internals of the ethernet.
  * It is easy to substitute the layers with alternative implementation. For example, if DAO layer is designed properly, if should be easy to change the ORM solution from [Hibernate][3] to [iBatis][4] easily.
  * Layering also helps in standardization and extensibility.
But the downsides of layering applications include cascading changes, performance overhead of converting data from one representation into another and it is not often easy to decide the layer of a particular component. Considering the benefits of layering and it's applicability to almost any sort of computer application, it is used widely and successfully.

Three primary layers of an enterprise application can broadly be categorized into 

  1. Presentation Layer: Information display, HTTP requests, command line invocations, batch API, etc.
  2. Domain Layer: Here lies the "Heart of the software"
  3. Datasource Layer: Provides access to external resources like databases, messaging, mail server, etc.
In this blog, we will discuss various patterns of organizing domain logic and specific pros and cons of each. We will also discuss how these approaches compare with each other.  **Domain Login Patterns** In his book, [PoEEA][5], [Martin Fowler][6] has classified domain logic patterns into three primary patterns: Transaction Script, Domain Model and Table Module. He also discussed introducing an additional layer, Service Layer, which is useful in providing horizontal services. Let's look at each of these in detail.

**Transaction Script (TS)** Transaction script is essentially a procedure that takes input from the presentation, processes it with validations and calculations, stores data in the database, send emails, etc. It may also fetch the data and send to the UI. It's more like coding a C program in Java. Typically, in transaction script method of organizing domain logic, the code is segregated into various services and each service has methods that perform a particular business functionality. A method of a transaction service might look like this

[sourcecode language="java"]void tsmethod(params) { // get data // process data if () { } else if { } else { } // insert/update/delete data //send email }[/sourcecode]

As you can see from the above, most of the above code is procedural. Here, the services act on the entities. A typical example would be an interaction between session bean and entity bean. Another similar pattern not mentioned in the PoEEA is [Anemic Domain Model][7] (ADM) which works on the same principle where domain objects have attributes and relationships but does not have behaviour. Behaviour is provided by the services. The different between TS and ADM is that in former we do not have domain objects but rather have something which Fowler describe as [Table Data Gateway][8], which essentially is a wrapper for SQL queries.

**Domain Model (DM)** In domain model approach, both behaviour and data are encapsulated inside the domain objects. Domain model looks different from the database design and utilizes inheritance, strategies, and other design patterns to create complex webs of small interconnected objects. A typical domain model code might look like

[sourcecode lang="java"]class Bar { private BarAdmissionPolicy = new BarAdmissionPolicy(); public void admit(Person person) { if (BarAdmissionPolicy.isAllowed(person)) { this.add(person) } } } public class BarAdmissionPolicy { public boolean isAllowed(Person person) { if (person.age > 18) { return true; } return false; } }[/sourcecode]

A well designed domain model provides an abstraction of the domain logic. It encapsulates complex business logic as interactions between domain objects.

**Table Module (TM)** It is essentially a singleton instance that handles the business logic for all the rows in a database table. There is no concept of identity in a Table Module. It is designed to operate on entire table data. A typical domain might look like

[sourcecode language="java"]class Product { void insert(name, type, etc) { } void insertAll(List) { } void calculateProductCost(List productIds) { } }[/sourcecode]

As you can see, a table module encapsulates the data and behaviour of the entire table. It is most commonly used in places where UI widgets are data-aware like Oracle Forms or .Net environment which has Visual Studio, which provides tools like [RecordSet][9].

**Service Layer (SL)** A Service Layer defines an application's boundary and its set of available operations from the perspective of interfacing client layers. It encapsulates the application's business logic, controlling transactions, applying security and coordinating responses in the implementation of its operations. Service Layer can be thick or thin depending upon whether you are using transaction scripts or domain model.

Now, we have understanding of the various domain logic patterns, let's discuss how they compare with each other.

**Comparison** Essentially, if you are using one of DM, TM or ADM pattern, the domain layer can be split into two layers: SL + DM/TM/ADM. SL is essential in providing horizontal services like security, transactions, etc. With TS in practice, it is difficult to identify the difference between SL and TS since TS can double up as a SL also.

Since, TM emphasizes one class per table, there are issues in implementing OO concepts while inheritance. There are no direct relationships between objects. For example, if you want to get a List of Products and Categories separately then it is easy to define where to put the code, but it is not clear where to put the code if you need a RecordSet which contains both Products and Categories.

TS is more like a service class which makes implementing domain logic difficult when the complexity of the application increases, but works well for simple applications.

There is a tough competition between ADM and DM. DM is essentially a Rich Domain Model (RDM). Common things between them includes data and relationships. But things specific to RDM is behaviour of the domain model. There has been a heated debate between designers to prove the dominance of one over the other. Suprisingly, the wiki page for ADM lists why should be avoid them. I reiterate them here 

  * Logic cannot be implemented in a truly object-oriented way unless wrappers are used, which hide the anemic data structure.
  * Violation of the principals information hiding and encapsulation.
  * Necessitates a separate business layer to contain the logic otherwise located in a domain model. It also means that domain model's objects cannot guarantee their correctness at any moment, because their validation and mutation logic is placed somewhere outside (most likely in multiple places).
  * Necessitates a global access to internals of shared business entities increasing coupling and fragility.

   [1]: http://en.wikipedia.org/wiki/Transmission_Control_Protocol
   [2]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
   [3]: https://www.hibernate.org/
   [4]: http://ibatis.apache.org/
   [5]: http://www.amazon.com/Patterns-Enterprise-Application-Architecture-Martin/dp/0321127420
   [6]: http://martinfowler.com/
   [7]: http://martinfowler.com/bliki/AnemicDomainModel.html
   [8]: http://martinfowler.com/eaaCatalog/tableDataGateway.html
   [9]: http://en.wikipedia.org/wiki/Recordset
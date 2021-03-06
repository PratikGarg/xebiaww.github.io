---
layout: post
header-img: img/default-blog-pic.jpg
author: vrawat
description: 
post_id: 14945
created: 2012/09/23 19:07:57
created_gmt: 2012/09/23 14:07:57
comment_status: open
---

# Giving REST to your JPA Repositories

I was thinking to write this blog a month earlier, when I wrote my demo application using Spring Data Rest but due to project work I was not able to do it. Finally I've been able to get some time and write this blog. As per [springsource.org][1], 

> The goal of the Spring Data REST project is to provide a solid foundation on which to expose CRUD operations to your JPA Repository-managed entities using plain HTTP REST semantics. 

Basically it allows you to expose your JPA Repositories over HTTP as a REST layer with some simple and intelligent configuration. No hassles of writing a controller layer to expose the Repositories. Lets see how its done(assuming you have a working knowledge of **[Spring Data JPA][2]**). 

**The Code** Here is the link to complete project : [Github Repo][3] Here is the link to live application : **[Sprig Data Rest Example App**][4] (Hosting by [AppFog.com PaaS][5])

**Domain Classes and JPA Repositories** Below are the simple domain classes and corresponding repositories used in the example. **User Class** [sourcecode language="java"] package in.xebia.datarest.domain; @Entity public class User {
    
    
    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String userName;
    @RestResource(exported = false) 
    private String password;
    
        //getters and setters
    

} [/sourcecode]

**User Repository** ``` 
 package in.xebia.datarest.repository; public interface UserRepository extends JpaRepository<User, Long> { } 
 ```

**Post Class** ``` 
 package in.xebia.datarest.domain; @Entity public class Post {
    
    
    @Id @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;
    private String content;
    
    @ManyToOne
    private User user;
    
    //getters and setters
    

} 
 ```

**Post Repository** ``` 
 package in.xebia.datarest.repository; public interface PostRepository extends JpaRepository<Post, Long> {
    
    
    public Post findByUserUserName(@Param(&quot;userName&quot;) String userName);
    
    public Post findByTitle(@Param(&quot;title&quot;) String title);
    

} 
 ``` Please note in User class I have used **@RestResource(exported = false)** this is used so that password is not exported when requesting for a user. It Can be used with Repositories and query methods also.

Here is how I have configured my repositories : [hibernate-context.xml][6] and a minimal [applicationContext.xml][7]

**REST Export** Now in order to export your JPA Repositories you just add a custom **"RepositoryRestExporterServlet"** provided by spring in your **web.xml**. Below is the code snippet. [code language="xml"] <servlet> <servlet-name>exporter</servlet-name> <servlet-class>org.springframework.data.rest.webmvc.RepositoryRestExporterServlet</servlet-class> <load-on-startup>1</load-on-startup> </servlet>

<servlet-mapping> <servlet-name>exporter</servlet-name> <url-pattern>/*</url-pattern> </servlet-mapping> 
 ```

In the above code I have used **RepositoryRestExporterServlet **to map all incoming requests. You can also use RepositoryRestExporterServlet along with DispatcherServlet in case you want to add Spring Data Rest to your existing Spring MVC project.

In example application there are two repositories **PostRepository** and **UserRepository**. So two resources named **"post"** and **"user"** will be exposed by the example web application.

**CRUD using Data REST** Lets start with the following : **Request** [code language="css"]

curl localhost:8080/datarest 
 ```

**Response** [code language="css"] { "links" : [ { "rel" : "post", "href" : "http://localhost:8080/datarest/post" }, { "rel" : "user", "href" : "http://localhost:8080/datarest/user" } ], "content" : [ ] } 
 ``` The above response shows how many repositories are exposed and under which urls. 

Lets create a user. **Request** [sourcecode language="css" wraplines="true"] curl -v -d '{"userName" : "Vijay", "password":"123"}' -H "Content-Type: application/json" http://localhost:8080/datarest/user [/sourcecode]

This will create a user with userName as Vijay and password as 123. Now lets see how a post can be created(**Embedded Types**).

**Request** [code language="css"] curl -v -X POST -H "Content-type: application/json" -d '{"title":"Spring Data Rest", "content":"Spring data rest is cool", "user":{ "rel" : "user.User", "href" : "http://localhost:8080/datarest/user/1" } }' http://localhost:8080/datarest/post 
 ```

And now if we request for [code language="css"]http://localhost:8080/datarest/post
 ``` output will be [code language="css"] { "links" : [ ], "content" : [ { "links" : [ { "rel" : "self", "href" : "http://localhost:8080/datarest/post/1" }, { "rel" : "post.Post.user", "href" : "http://localhost:8080/datarest/post/1/user" } ], "content" : "Spring data rest is cool", "title" : "Spring Data Rest" } ], "page" : { "size" : 20, "totalElements" : 1, "totalPages" : 1, "number" : 1 } } 
 ```

For querying on custom methods like **findByUserUserName(String userName)** defined in our postRepository. We can write a query like this: [code language="css"] curl -v http://localhost:8080/datarest/post/search/findByUserUserName?userName=Vijay 
 ```

Finally deleting a post using the following request: [code language="css"] curl -i -H -X DELETE http://localhost:8080/datarest/post/1 
 ``` This will delete the post with id equal to 1. 

Thats it for now guys. Hope you like it. 

**Note:** Since this project is actively under development and **RC3 **is the latest Release Candidate at the time of writing. Please change versions accordingly.

   [1]: http://www.springsource.org/spring-data/rest
   [2]: http://www.springsource.org/spring-data/jpa
   [3]: https://github.com/vijayrawatsan/Spring-Data-Rest
   [4]: http://springdatarest.ap01.aws.af.cm
   [5]: http://www.appfog.com/
   [6]: https://github.com/vijayrawatsan/Spring-Data-Rest/blob/master/src/main/webapp/WEB-INF/hibernate-context.xml
   [7]: https://github.com/vijayrawatsan/Spring-Data-Rest/blob/master/src/main/webapp/WEB-INF/applicationContext.xml
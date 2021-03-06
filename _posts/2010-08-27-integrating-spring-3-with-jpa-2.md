---
layout: post
header-img: img/default-blog-pic.jpg
author: rjaiswal
description: 
post_id: 4584
created: 2010/08/27 17:20:20
created_gmt: 2010/08/27 12:20:20
comment_status: open
---

# Integrating Spring 3 with JPA 2

I have been working on Grails recently and am amazed with the productivity gains it gives. Grails is great for giving you a kick-start, it uses convention over configuration and gives you things such as ORM, transaction management, dependency injection among others, without you writing a single line of configuration.

I decided to mimic the Grails behavior in a Spring + Hibernate application. To stay on the cutting edge I decided to use Hibernate 3.5 and Spring 3, to top it up I decided to use Hibernate as an implementation of JPA 2. Recently released, Hibernate 3.5 combines the Hibernate Annotations and the Hibernate Entity-Manager projects into Hibernate-Core, so basically its just one jar which provides the full implementation of JPA 2. For advantages of using JPA 2 see the excellent presentation here - <http://jazoon.com/portals/0/Content/ArchivWebsite/jazoon.com/jazoon09/download/presentations/8461.pdf>

**Integrating Hibernate with JPA 2**

As usual, I started with a vanilla maven web project and started adding dependencies, the first being Hibernate - [code language="xml"] <dependency> <groupId>org.hibernate</groupId> <artifactId>hibernate-core</artifactId> <version>3.5.4-Final</version> </dependency> 
 ``` This dependency is found in the repository - <https://repository.jboss.org/nexus/content/groups/public/> as mentioned on the Hibernate site. After a lot of pain I found that this jar did not match the jar you from <http://sourceforge.net/projects/hibernate/files/hibernate3>. So, I had to manually install the jar in my local repository (remember, I found this out after setting up JPA, Spring etc. which I will cover next). Similarly, in the JBoss repository I could not find the JPA 2 specfication jar (again available if you download the Hibernate distribution from sourceforge).I had to install that manually as well. After all this jiggery-pokery and some more, I somehow got Hibernate and JPA working.

JPA needs a persistence.xml file in META-INF folder in the classpath. I added META-INF folder in source/main/resources with persistence.xml reading as - [code language="xml"] <persistence xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/persistence http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd" version="2.0"> <persistence-unit name="postage" transaction-type="RESOURCE_LOCAL"> <provider>org.hibernate.ejb.HibernatePersistence</provider> </persistence-unit> </persistence> 
 ``` So, here I basically tell JPA that Hibernate is providing its implementation. Plus I give my persistence-unit a name "postage" (the name of my application).

**Integrating Spring with JPA**

Now, I need to get Spring working with JPA and it is not exactly a cakewalk. With a lot of people pointing in different directions and the Spring documentation itself not very helpful in this particular aspect.

The Spring JPA support itself offers three ways of setting up the JPA EntityManagerFactory, so that it can be used by the application to obtain an entity manager. I chose the "LocalContainerEntityManagerFactoryBean" because it gives full control over EntityManagerFactory configuration and is appropriate for fine-grained customization, if required. Below is my config - [code language="xml"] <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean" p:dataSource-ref="dataSource"> <property name="jpaProperties"> <props> <prop key="hibernate.dialect">${hibernate.dialect}</prop> <prop key="hibernate.hbm2ddl.auto">${hibernate.hbm2ddl.auto}</prop> <prop key="hibernate.show_sql">${hibernate.show_sql}</prop> </props> </property>   
</bean> 
 ``` As evident, you need a "dataSource" bean defined, which looks like - [code language="xml"] <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource" p:driverClassName="${dataSource.driverClassName}" p:url="${dataSource.url}" p:username="${dataSource.username}" p:password="${dataSource.password}" /> 
 ``` To get the "${}" thingy working, i.e. a properties file lookup, add - [code language="xml"] <context:property-placeholder location="classpath:datasource.properties"/> 
 ``` datasource.properties can look like - ``` 
 dataSource.driverClassName=org.hsqldb.jdbcDriver dataSource.url=jdbc:hsqldb:mem:postage dataSource.username=sa dataSource.password= #Hibernate Properties hibernate.hbm2ddl.auto=create-drop hibernate.dialect=org.hibernate.dialect.HSQLDialect hibernate.show_sql=true 
 ``` To get transactions working, add - [code language="xml"] <tx:annotation-driven/> 
 ``` And define a transaction manager - [code language="xml"] <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager" p:entityManagerFactory-ref="entityManagerFactory" /> 
 ``` Now, assuming that you have an Entity "Post", lets create the DAO - ``` 
 @Repository @Transactional public class PostageDAOImpl implements PostageDAO {
    
    
        @PersistenceContext
        private EntityManager em;
    
        public void savePost(Post post){
            em.persist(post);
            em.flush();
        }
    
    }
    


 ``` The @Transactional annotation makes the DAO transactional (remember we added ). Let's look at the other Annotations - @Repository annotation is a "stereotype" annotation added in Spring 2.5 which enables Spring to identify Beans for AOP pointcuts. In our case @Repository annotation also enables Spring to automatically convert JPA exceptions to Spring exception hierarchy. Finally, the EntityManager is automatically injected by Spring as it is annotated by @PersistenceContext. Also add - [code language="xml"] <context:component-scan base-package="net.rocky.postage"/> 
 ``` This tag enables dependency injection with annotations and also auto-detects stereotype classes in the specified package and registers the corresponding beans. 

That's it! We have integrated Spring with JPA 2 and Hibernate. Let's write a test case to verify our setup - ``` 
 @RunWith(SpringJUnit4ClassRunner.class) @ContextConfiguration("/applicationContext.xml") public class PostageDAOImplTest {
    
    
    @Resource
    private PostageDAO postageDAOImpl;
    
    @Test
    public void testPostStorage() throws Exception {
        Post post = new Post();
        post.setComments(&quot;A simple comment&quot;);
        post.setDescription(&quot;A simple description&quot;);
        postageDAOImpl.savePost(post);
    }
    

... 
 ```

**Parting thoughts**

This whole exercise took me a long time due to lack of comprehensive documentation, version mismatches and multiple approaches suggested by documents/bloggers. The advantage of the approach I have suggested above is that it allows you to switch to different databases for test/production as the datasource and dialect is specified in a .properties file.

The big idea behind this exercise was to setup a DAO and domain layer akin to Grails which can be easily scaled up. We can further add a service layer to access the data and then use it over a UI. For my next blog I will build on this and create a web application that uses GWT as the front-end.

## Comments

**[Rocky](#3603 "2010-12-15 00:22:46"):** Hi R D, Thanks. Haven't really done this on JBoss 5. Will try and do so once I get some time from my project :).

**[Max](#3243 "2010-11-16 03:46:05"):** Thank You! That is exactly what I was looking for :)

**[R D](#3459 "2010-12-09 23:50:27"):** Hey Rocky, Nicely done. Were you able to deploy your application in JBoss 5.1 or 6.0 CR1 server? If yes, could please share your experience? Regards

**[Pieter](#3020 "2010-10-12 13:29:00"):** Hey Rocky, very nice and to the point post! You are describing virtually to the point exactly the same situation as I find myself to be in at the moment. So I found this a very interesting read. I have however a question, What maven dependencies did you add towards the Spring integration? Or, if possible, you could send me the list of dependencies you used?

**[mondraymond](#5988 "2011-10-06 22:29:04"):** Great work dude... it got me out of a hole, so thanks. One word on what I ended up with, rather than using the you can also consider: This had the advantage for me that I could configure the beans in the context where as the package scanner autowires everything.

**[mondraymond](#5990 "2011-10-06 22:32:20"):** Oops, your blog doesn't take markup. Shame... Anyway, I was pointing towards: org.springframework.orm.jpa.support.PersistenceAnnotationBeanPostProcessor

**[Daniel](#5787 "2011-07-29 21:34:38"):** Hi Rocky, thanks a lot for this good and precise tutorial. It worked very good, except for some problems with Transaction: I marked methods with @Transactional, after running the JUnit Test I get a org.springframework.beans.factory.BeanNotOfRequiredTypeException Bean named 'xxxDaoImpl' must be of type [com.westernacher.wps.bnotk.fibu.dao.XxxDaoImpl], but was actually of type [$Proxy23] I have experimented with the settings tx:annotation-driven proxy-target-class="false" and mode="proxy", but it gave different errors. Do you have any idea about this? Best Regards, Daniel

**[David](#8633 "2012-04-30 17:49:44"):** Hello, Could you post a full example, please? Thank you. David


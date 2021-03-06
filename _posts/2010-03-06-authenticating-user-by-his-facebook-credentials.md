---
layout: post
header-img: img/default-blog-pic.jpg
author: sunil.inteti
description: 
post_id: 3223
created: 2010/03/06 17:17:12
created_gmt: 2010/03/06 12:17:12
comment_status: open
---

# Authenticating user by his Facebook credentials

In my previous [post][1] I talked about how we can connect to Facebook from our application and get the user deatils. In this post we will discuss how we can authenticate the user based on his facebook user details. We will  use  acegi plugin of grails to allow uses our of web application to be authenticated by their facebook credentials . We dont need to aunthenticate them into our application like normal way. Let us see how that can be done!

The prerequisite for this feature is to have the acegi plugin installed. In my last [post][1] , after the getting the facebook user details we redirect to facebook controller passing his details(**facebook user id, sessionKey** etc) as params :

[sourcecode lang="java"]document.location.href="${params.lang}/facebook/subscription?firstname="+firstname+"&lastname="+lastname+"&fbuid="+uid+"&sessionkey="+sessionkey+"&pic="+pic;[/sourcecode]

Here we are passing the facebook user id as **fbuid. **Assuming the user class in the application is **UserAccount, **we can have a fbuid property on UserAccount. From the param **fbuid** we can get the existing user.We can also choose to connect **fbuid** to an existing user in our application. For that we may need to authenticate user normally into our app and then updating his **UserAccount.fbuid **from the param fbuid. We can use a popup mechanism to give the user the option to connect his facebook uid to his existing account or create a new account with his facebook user id.

Lets assume that we already have an Useraccount and we have its correspondint fbuid attached to it. How can we create his authentication object. Here is the code which can be part of as service class(utilityService for eg.)

In **FacebookController**

[sourcecode lang="groovy"]

subscription = {

def account = UserAccount.findByFbuid(params.fbuid) if (account) { // assuming user has list of authohorities or roles ... utilityService.authenticateFacebookUser(params.fbuid, params.sessionkey, account.authorities) } } [/sourcecode]

The idea here is that from **fbuid **we can get his account(Since user has a fbuid property). From that we can get his authority and then authenticate him using the following method in utilityService.

[sourcecode lang="java"] public void authenticateFacebookUser(long uid, String sessionKey, GrantedAuthority[] authorities) { def token = new FacebookAuthenticationToken(authorities, uid, sessionKey) FacebookAuthenticationToken authentication = facebookAuthProvider.authenticate(token) SCH.context.authentication = authentication } [/sourcecode]

Here **FacebookAuthenticationToken **and **facebookAuthProvider **are the classes provided by acegi grails plugin & SCH is the alias for Security Context Holder. By this way an user can be linked with his facebook id and authenticated.

How ever there are some changes that could be needed. In our existing application we are using acegi plugin and we use the AuthorizeTagLib.grrovy. We have to extend this to include the facebook authenticated users. Other wise when we use this existing logic we will get missing property exception on **aunthentication **object, as the authentication object is different in case of facebook and normal authentication object.

For example the isLoggedIn() method in AuthorizeTagLib.groovy can be extended like this.

[sourcecode lang="groovy"]

public boolean isLoggedIn() { def authenticationObject = SCH?.context?.authentication return (authenticationObject instanceof FacebookAuthenticationToken || authenticateService.isLoggedIn())

}[/sourcecode]

As the case may be other methods of the AuthorizeTagLib may have to be extended as well. This applies for classes facebookAuthenticationProvider and UserDetailsService of Acegi plugin.By this the user of our application can be authenticated by his facebook credentials and he can use our site normally.

**Summary:**In this post we basically talked about the idea of connecting a userAccount to user's facebook id, and authenticating  user by his facebook credentials also.

   [1]: http://xebee.xebia.in/2010/03/06/connect-to-facebook-in-grails-app/
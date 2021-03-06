---
layout: post
header-img: img/default-blog-pic.jpg
author: sraheja
description: 
post_id: 10553
created: 2011/12/28 10:27:43
created_gmt: 2011/12/28 05:27:43
comment_status: open
---

# How to conceptualize a Location Aware Application?

With availability of location services in all upcoming smart phones, a number of location aware applications have hit the market. [Groupon][1], [Foursquare][2], [Loopt][3], [Roamz ][4]and many more are now popular names among users.

First of all, let us try to understand usefulness of such applications. Location aware applications are intuitive and have much better user experience. Let us say you are travelling and would like to know services and facilities available around you. All you have to do is open an application on your phone and will get to know about various eating outlets around you and much more.

Location awareness has enabled a number of new use cases. Things you earlier could have never thought off. User's location can be used to create solutions for various verticals: 

  * **Navigation**: Google Maps, TomTom aid users in turn by turn navigation.
  * **Networking**: Foursquare helps you in finding friends currently in your neighbourhood.
  * **Local information about facilities and services**: Roamz shows you social content about places, events and activities around you.
  * **Geo marketing**: Groupon features deals on the best stuff to do, see, eat and buy in your favourite neighbourhoods.
  * **Lifestyle**: Location based reminders are now part of iOS5.
  * **Games**: TrezrHunt is based on real locations where you have to find your way to witch Armagal's treasure.
and many more. The benefits are immense for end users as well as business owners.

However, despite so many benefits, adoption rate of such applications is not catching up at the rate expected. According to a [new report from Forrester Research][5], _out of 37000 U.S. online adults, only about 5% of users use location aware applications once a month_.  **Concerns** The major concern of the users is their**_ Security and Privacy_**. They are not comfortable with idea that some one is watching them or someone is trying to look inside their head to understand their shopping behavior.

I have been working on location based solutions for quite some time now. Here are my findings about how such an application should be conceptualized and built so that it does not hinder with user's privacy and is readily accepted among them. 

  * **Awareness** First and foremost, a user should be well informed about the purpose of the application. How his location will be used to achieve the desired results? He should be made aware of optional and configurable features in the application. 

Most of the smart phones do show a permission dialog as soon as application tries to use location for the first time. Sometimes it is not immediately apparent to the user why the application will use his location. If, at that,moment user disallows use of his location, your application will never get the location access. Unless he explicitly allows it from phone's settings, which is less probable.

So, one should always add appropriate information in the application description available on app store. iOS lately has introduced a customizable "Purpose Property" that is shown in the permission dialog required for authorization. One should make full use of such features to make user understand the context and benefits.
  * **Opt-in Tracking** User should be able to turn off location tracking as and when he wants. He should be able to define criteria when his location should be tracked. For example, he may like to do location aware networking during a trade fair, not when he is celebrating his marriage anniversary. Another example can be location aware services should be turned off at night when he is sleeping.User should also be able to configure accuracy level to achieve the desired results, thereby preserving his privacy to an extent and reap benefits as well.
  * **Opt-in Usage** User should be able to configure how his location should be used. For example, in geo marketing applications, user should be able to choose the brands from whom he should receive offers or on his social network, he should be able to filter his location updates among his friends. Some of the latest applications are mining location information to predict users' future locations. Such features should be optional and configurable.
  * **Secure** We should understand that location data is quite sensitive and personal, so if possible, the application should never store it. However, if not, application should either use one of the anonymization techniques or encrypt it before storing user's location specific data. Applications can also use obfuscate location by degrading the quality of information about an individual's location in order to protect his privacy.
  * **Accurate** The location detection algorithm should be accurate. It should take care of inaccurate and false readings. Some of the platforms do give cached locations till GPS hardware warms up. One should handle them appropriately. This is more on the implementation side, I will share my thoughts and techniques on this in a separate blog.
  * **Optimized** Finding accurate location is a battery draining operation for mobile phones. So, the solution should be optimized for battery consumption as per the requirements. Lets say for an application aiding user in turn by turn navigation, higher accuracy is needed along with frequent updates. However, for local weather updates, lower accuracy mode can also achieve the desired results. 

Generally most of the periodic location aware solutions are either displacement based or time based. What that means is application will get the next location update from the device after x meters of displacement or after t seconds of time interval. One can play with these parameters and make them dynamic on each subsequent update to optimize battery usage
**Conclusion** These are some of the basic concepts that one should incorporate while creating a location aware application. It is easy to fall into short term benefits trap by overlooking them, however, in the longer run, adhering to these fundamentals will ensure more acceptability among users.

   [1]: http://itunes.apple.com/us/app/groupon/id352683833?mt=8
   [2]: http://itunes.apple.com/in/app/foursquare/id306934924?mt=8
   [3]: http://itunes.apple.com/us/app/loopt/id281952554?mt=8
   [4]: http://itunes.apple.com/us/app/roamz/id459343660?mt=8
   [5]: http://www.forrester.com/rb/Research/marketing_via_geosocial_apps_why_and_how/q/id/61072/t/2
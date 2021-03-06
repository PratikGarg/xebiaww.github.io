---
layout: post
header-img: img/default-blog-pic.jpg
author: jitens
description: 
post_id: 16172
created: 2013/03/19 17:49:36
created_gmt: 2013/03/19 12:49:36
comment_status: open
---

# Blocking Net-Banking Accounts En Masse

**HDFC Bank Website Vulnerable to ID Theft and Accounts Blockade.**

The NetBanking service of HDFC Bank, which is one of the largest and most reputed Banks in India, is completely vulnerable to ID theft, Targeted phishing and Mass Account Blockade. HDFC bank has implemented an extra security solution called 'Secure Access' on its website but instead it has given a hidden door to hackers to block all HDFC NetBanking accounts. 'Secure Access' is an additional layer of security implement on HDFC Bank website is essentially a solution protecting your account from hackers and fraudsters. Secure Access, an online security initiative is intended to make NetBanking transactions safer by protecting users from phishing websites and Hackers and help users authenticate original HDFC website. HDFC Bank's NetBanking login is now a two-step process with Secure Access image and text verification. Let us take you through the steps involved in Secure Access and how it intends to protect NetBanking account of HDFC customers.

1) In First Step User has to provide his customer-id (User Id) on the first screen

2) User is then taken to the next screen where he is shown his personalized picture and message and asked to enter his IPIN (password).

[caption id="attachment_16177" align="aligncenter" width="514"]![HDFC NetBanking Login page][1] HDFC NetBanking Login page[/caption]

Demo presentation of secure access work can be seen on HDFC website at following URL <http://www.hdfcbank.com/assets/demo/secure_access.swf > Secure Access which is claimed to be Multi-layered Authentication Process mandatorily requires HDFC NetBanking users to register for it if they want to conduct ANY Third Party Transactions like Transferring Money, booking movie tickers, recharging phone.

During the research conducted in December on HDFC Mobile Banking application and NetBanking Portal it was discovered that while implementing Secure Access HDFC Bank has compromised on the first and foremost principle of user authentication on any Banking website i.e. Never reveal whether its User Name or the password which is incorrect in case wrong combination is entered. Since Secure access implementation requires user to only enter his Customer ID (The fixed user name in case of HDFC bank NetBanking users. The system then checks at the backend if the user is registered for Secure Access or NetBanking, if user is not a registered or valid user, Customer id is asked again. But in case if the customer id is found to be correct and registered, user is taken to second step and shown his secure access image and text he selected and asked to enter is password.

  1. Now this 1st step of verifying customer id and showing personalized secure access image and text has made HDFC Bank vulnerable to Blind customer id collection. We exploited this vulnerability by running an automated suite with by generating random customer ids in a sequence (example from 434XXXX to 515XXXXXX) and logged on HDFC NetBanking website using them. The customer ids which showed secure access and image and text were noted and rest were discarded. The secure Access images and text which was shown for valid customer ids were also saved. Now by the process above we were able to achieve 3 things. It was shocking to see that secure text was displayed as simple text and not a text image.

By this automated first step of Login, we were able to create a Database of several customer Ids of HDFC NetBanking users. We could have made a complete database of every NetBanking users but since this trial was conducted with a motive of checking security on banking site, only a sample data was collected. We decided to destroy this data later after completing this Demonstration.

**HDFC Phishing Mobile App using our database of secure Access images and Text**

[caption id="attachment_16180" align="aligncenter" width="349"]![HDFC Mobile App][2] **HDFC Mobile App**[/caption]

**Customer Id, Personalized Image and Text of Customer in above Screenshots has been hidden for security**

[caption id="attachment_16178" align="aligncenter" width="515"]![HDFC Login Page Second Step][3] **HDFC Login Page Second Step**[/caption]

2) We then setup a phishing portal for HDFC Net banking and created a fake HDFC NetBanking Mobile app and tried it on some people. In our fake HDFC mobile application and phishing portal we were able to replicate exactly same Secure Access behaviour like on original HDFC website using the database of Customer IDs of NetBanking users and their secure Access image and text we had created earlier. Since our site was showing the same secure Access images and text on entering customer ids we used their security feature to provide more authentic look to our phishing website and Mobile application. The Situation was worse for fake mobile app since Mobile users could not even see the URL, setting aside any chance of doubt in mind of user. Several users trusted our phishing site and mobile app and tried to log in with their passwords. 3) Now in a third Killing step we launched denial of service attack on website the by blocking some user accounts. Since Secure Access authentication vulnerability has helped us create the database of customer ids of HDFC NetBanking users we launched another program which simple went on HDFC NetBanking website and provided customer id from our database on HDFC NetBanking login page. As we were providing valid Customer Id's, when asked for IPIN/Password we entered it incorrectly 5 times which resulted in those customers being blocked from NetBanking. We used chain proxy to bypass time and Sequence checks but were surprised to find out that HDFC site had NONE and we were able to easily block several user accounts.

[caption id="attachment_16179" align="aligncenter" width="565"]![HDFC Account Blocked][4] **HDFC Account Blocked**[/caption]

Since we were guided by ideology of ethical hacking we conducted above research on a sample data But This flaw in authentication can be easily used by anyone to generate a Database of Customer Ids of HDFC NetBanking Users and completely Block all NetBanking accounts of all HDFC NetBanking users. The same may be the used by the miscreants or competitors of HDFC bank to hold literally entire NetBanking service of HDFC bank for ransom by repeatedly blocking accounts even if they are enabled again. This vulnerability also raises a big question, Are We forgetting basic principles of Security in race of trying security solution?

   [1]: http://xebee.xebia.in/wp-content/uploads/2013/03/hdfc-1.png (HDFC NetBanking Login page)
   [2]: http://xebee.xebia.in/wp-content/uploads/2013/03/hdfc.png
   [3]: http://xebee.xebia.in/wp-content/uploads/2013/03/hdfc.jpg (HDFC Login Page Second Step)
   [4]: http://xebee.xebia.in/wp-content/uploads/2013/03/HDFC2.jpg
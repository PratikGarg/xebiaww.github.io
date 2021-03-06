---
layout: post
header-img: img/default-blog-pic.jpg
author: balajidl
description: 
post_id: 436
created: 2008/03/05 20:45:49
created_gmt: 2008/03/05 18:45:49
comment_status: open
---

# Flex Beyond -- eForms

Around 6 months back me and [Vikas Hazrati][1] gave a [XTR][2] on Adobe Flex to my colleagues in Xebia India.

We took some resources from [Adobe Dev Net ][3]site and eventually found a cool [article][4] explaining how Flex Data Services works with Spring using Spring Remoting features and so on. We were quite amazed with the UI capabilities of Flex with its server side integration. If you look at that article now, its start with a disclaimer saying "Effective with the release of Adobe LiveCycle ES, the Adobe Flex Data Services 2 server product has been rebranded as a Solution Component of LiveCycle ES." 

I did heard about [Adobe Livecyle ES][5] before but at that time it was called as **Adode Document server**. Adobe keeps changing its product name for reasons only they can understand & agree, but the point is that the architecture and the concept of the Adobe LiveCycle ES is getting very matured and stunning.

**What is Adobe LiveCycle ES** ?

> Adobe® LiveCycle® ES (Enterprise Suite) software is an integrated J2EE server solution that blends electronic forms, process management, document security, and document generation to help you create and deliver rich and engaging applications that reduce paperwork, accelerate decision-making, and help ensure regulatory compliance. Read more from http://www.adobe.com/products/livecycle/

**Ok, now what Flex has to do with LiveCycle ES ?** Just have a look at the below diagram showing the technology summary of LiveCycle ES. ![][6] [Image linked directly from the site Avoka]

If you look at it carefully and if you are a vivid Java developer, you might have noted that the server-side of LiveCycle is based on J2EE. LiveCycle reckons using Flex or PDF as its UI. **What!...PDF as an UI?, but how can it provide a rich interface that Flex or HTML can give ?** Yes. Have a look at [Adobe LiveCycle Designer][7] . **What is Adobe LiveCycle Designer**

> Using Adobe® LiveCycle® Designer ES software, you can create form and document templates that combine high-fidelity presentation with XML data handling. This gives you the ability to create dynamic forms that closely mirror the paper forms they will replace. A unified design environment with an intuitive graphic interface makes it easy to quickly design and maintain templates, define business logic, and preview them in real time before they are deployed. Read more at http://www.adobe.com/products/livecycle/designer/

While you can use Flex for 'shopping cart' kind of user interactions, you can use PDF (precisely called Interactive PDF) for form filling and submission interactions.

**Well!, Where this can be useful and why this sounds like a complex solution ?** Actually no. The Flex and PDF is just a small part of the LiveCycle ES and the other components in LiveCycle ES together provide solution for Enterprise Business Process Automation, RIA, eForms, Financial Services, Governments, Manufacturing and so on.

IMHO, one of the marketing factor for LiveCycle ES product is PDF. Be it manufacturing business process or Financial or eBusiness, you might require to produce a paper during the workflow. for example Commercial Invoice, receipts, Purchase order and so on. PDF is the best way to reproduce a paper document in electronic format. Adobe Reader is installed in 80% of PC (as per Adobe). This enables the end users of LiveCycle ES, especially the SME's to participate in workflow as a client using free Adobe Reader.

If possible I will try to cover more details about the LiveCycle ES components on my next blog.;)

   [1]: http://vikashazrati.wordpress.com/about/
   [2]: http://www.xebiaindia.com/in/your-career/our-culture/your-colleagues
   [3]: http://www.adobe.com/devnet/
   [4]: http://www.adobe.com/devnet/flex/articles/java_testdrive.html
   [5]: http://www.adobe.com/products/livecycle/
   [6]: http://www.avoka.com/ad_livecycle_es/images/marketecture_diagram_558x340.jpg
   [7]: http://www.adobe.com/products/livecycle/designer/
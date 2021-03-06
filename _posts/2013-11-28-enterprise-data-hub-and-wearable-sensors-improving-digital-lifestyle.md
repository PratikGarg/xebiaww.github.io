---
layout: post
header-img: img/default-blog-pic.jpg
author: harpreetsingh
description: 
post_id: 17667
created: 2013/11/28 13:23:02
created_gmt: 2013/11/28 08:23:02
comment_status: open
---

# Enterprise data hub and wearable sensors - improving digital lifestyle

In May 2013 report titled “**_Disruptive technologies: Advances that will transform life, business, and the global economy -  Michael Chue et. al. ”, _**, McKinsey documented 12 technologies areas that will massively impact society and our evolution. Report denominated the total value of impact of these technologies in the range of tens of trillions of dollars per year. Although its hard to predict future but the analyis was done on recent advents and technology impact on social and economic indexes.

First in the list of these technologies is the **_Mobile Internet_** that covers increasingly inexpensive and capable mobile computing devices and internet connectivity.  It is expected that by 2015, wireless web use will exceed wired use. These devices are helping people in interacting with the physical world. New formats of wearable devices are linking human digital data with applications and have capacity to distrupt and help us in learning more about human state & behaviour and collaboration. In the current paper we will be focusing on wearable sensor portion of Mobile Internet technology.

Advances in the areas of Semiconductor (MEMS), Software (Cloud and Big Data) and Communication (Low Cost Bluetooth) are enabling wearable sensors and brought intelligent watches for consumers. We anticipate that further advances in NEMS (Nano-electromechanical devices) will further improve the form factor. Also with advents in Big Data (Hadoop and NoSQL domain), it will have strong business impact. In our efforts to follow this innovation of wearable sensors, we are investigating two digital watches i.e. Metawatch (from Texas Instrument) and Gear (from Samsung) in learning the available technology landscape and evaluating their usage in two use cases that link real time human data with its potential in improving digital lifestyle. Both the devices have Bluetooth capability to connect with the cell phone devices and connect to centralized cloud application to transfer the recorded data.

Our first use case we are trying to link real time accelerometer data along with body temperature in improving detection of sleep and understanding relationship of stress and physical activity with psychological and physiological health in young children.

Our Second use case concentrates on building banking application for routine operations i.e. checking balance, recent transactions and act as a recipient for urgent notifications related to fraud detection. We anticipate that by recording real time behaviour data of users and monitoring the usage of existing bank applications, we will be able to improve customer retention in future.

The data from the devices goes over Bluetooth 4.0 via Android platform connected to cloud based NoSQL solution.  Encrypted JSON data packets are used for data communication on real time basis.  Data received on centralized repository is routinely crunched and analyzed via Hadoop Map & Reduce platform to extract information as per business requirement. Figure 1 highlights the high level data flow and solution schematics:

![wearablesensor][1]

**Figure 1: Wearable device solution schematics**

We have finalized on Android mobility platform for the above use cases due to its adoption and availability of SDK’s for data communication. Mongo NoSQL database is being used for real time data collection over cloud and chosen for its scalability and ease of deployment. Apache Hadoop based data crunching is being used for forming baseline data and finding business specific information to be routed back to device as per the need of business case.

Data collection is done via device at configurable time points over period of week/months. Without intruding into the lifestyle of the individual,we plan to do better detection of data and its relationship with use case. For example in case of detection of sleep, we use MEMS accelerometers to record the children realtime activity, body temperature and ambient light detection for environment conditions. The data is first de-identified at the device level and then encrypted before being sent to the cloud environment.

Next section of the paper will discuss individual technology pieces that are required in both business cases (common platform code). This will cover watch based android code to capture sensor data and communication API to send data to nearly cellular device that is already paired. There is a Android application on the cellular device that is responsible to listen and record watch data. On completion of the recording interval the data is sent to NoSQL database on Cloud via mobile or wifi network.

In the next blog, we will explain data analytics procedures that are used to extract information out of the data.

To know more about Xebia's offerings on Big Data, [Click here][2]

To know more about Xebia's offerings on Enterprise mobile, [Click here][3]

   [1]: http://xebee.xebia.in/wp-content/uploads/2013/11/wearablesensor-1024x736.jpg
   [2]: http://www.xebia.in/big-data.html
   [3]: http://www.xebia.in/enterprise-mobile.html
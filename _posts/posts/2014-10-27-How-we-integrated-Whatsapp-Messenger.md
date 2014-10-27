---
layout: post
title: How we integrated WhatsApp Messenger
excerpt: "This post explains how MySmartPrice integrated WhatsApp to provide best price comparison through text messages."
modified: 
categories: posts
author: arpit
tags: [WhatsApp, Integration, Redis]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

##A Brief Introduction About The Feature
This feature provides best prices compared across myriads of e-stores instantaneously just by whatsapping the product name to a specific number (933 222 2222) which is making shopping as easy and smart as possible.  

You can find more information about the service at [YourStory] and [Trak.in]

> Note: We brought the service down as our application was against the WhatsApp's EULA _(End User License Agreement)_. Please refrain from implementing this feature as it might get your phone number blocked by <i class=""></i>WhatsApp.

## Implementation Details 
We used unoffical WhatsApp API (*[WhatsAPI]*) which simulates a whatsapp client through PHP code. Now let's take a look at the technical details of this integration.

In Short, 

1. We connected to WhatsAPP server using the WhatsAPI. The whatsapp_id, EMEI number are the required credentials for the API to make successful connection.

2. A PHP process was written which uses the API and receives the message. 

3. The Process then creates a Hash Key in Redis along with all the information and adds the auto-incremented message_id to request queue implemented using Redis List.

4. A worker is spawned for every message which fetch the message id from the queue and then processes the search queries using our Search API. On success, adds the response back to the Hash key and adds the respective message_id to response queue.

5. Now our main process fetches the response from Redis and sends the response back to the phone number from which the request originated.  
  
<!-- ![work_flow_image](http://b7002b41b24df693b1d7-3fce2a06e03172c5e2e045b2102b8526.r18.cf1.rackcdn.com/WhatsApp%20Flow%20Chart.png) -->
<img src="http://b7002b41b24df693b1d7-3fce2a06e03172c5e2e045b2102b8526.r18.cf1.rackcdn.com/whatsapp%20flow%20chart.png" alt="work_flow_image" style="width: 625px;"/>

## Performance Stats
Under load, the time taken from message receive to responding with search results took less than 2 seconds on average.
  * As a warm up message was delivered almost instantaneously within the first second itself.
  * Calling our Search API and giving the top results took place within another seconds.

During our analysis, we found that significant amount of this time (2 Seconds) was spent on processing and parsing the WhatsApp message which was done by the WhatsAPI.

## Usage Stats
The adaption was quite good. Few search terms which we received made us rethink how we handle search in our system. Honestly, there are people who did SMS instead of WhatsApping to the number (means SMS is not dead yet). Back to topic, few metrics below.

* Approximately **15K** requests per day were processed by our system.
* Some of the most popular queries with average number of hits made were as following:-

     Queries    | Avg. Hits/Day
--------   | :---:
Iphone 6   | 580
Iphone 5S  | 504
Moto G     | 350
Nexus 5    | 208
Iphone     | 194
Samsung    | 186
Iphone     | 194

Yes, We saw a huge interest in iPhone among the people which is quite fascinating. 

## In The End
We found this integration of product prices with whatsapp very useful which has huge potential. Hope WhatsApp will come up with some form of official API in the future.
We are glad that we got an opportunity to work on such revitalising project that somewhat feeds the fuel that we need for the fire.[^footnote]  

PS: Thanks to [RG Dixit] who contributed to the initial development of the project.

[RG Dixit]: https://www.facebook.com/rgdixit.awsm
[YourStory]: http://yourstory.com/2014/09/mysmartprice-whatsapp 
[Trak.in]: http://trak.in/tags/business/2014/09/26/mysmartprice-whatsapp-pr
[WhatsAPI]: https://github.com/venomous0x/WhatsAPI

[^footnote]: [Eminem - "The Way I Am"](https://www.youtube.com/watch?v=Sd6pRP081cs&t=2m40s)  
	But I'm glad 'cause they feed me the fuel that I need for the fire  
	To burn and it's burning and I have returned.


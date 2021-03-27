---
title: Methodology To Perform Web-socket Pen testing in my way
description: 'Hello All,'
date: ''
categories: []
keywords: []
slug: ''
---

  

Hello All,

Today we are going to read about Websocket and its Security Pentesting

The WebSocket is an advanced technology that makes it possible to open a two-way interactive communication session between the user’s browser and a server. With this API, you can send messages to a server and receive event-driven responses without having to poll the server for a reply.

Ater digging Deep into Websocket ([http://www.websocket.org](http://www.websocket.org) ) We have discovered below methodology to Perform Websocket Pentesting

For More Information of Websocket available on

[http://www.websocket.org/aboutwebsocket.html](http://www.websocket.org/aboutwebsocket.html)

**Methodology To Perform Web-socket Pen testing** 

1.  Identify the websocket URL
2.  Check for Cross socket websocket Hijacking (Vulnerability)
3.  Capture the all Websocket Frames using burp proxy for manual testing like IDOR,Privilege Escalation,Data Tampering

**Steps to Identify Websocket in Application**

We can check the Websocket URL using **Browser Inspector or burp suite proxy**

1)Browser Inspector-> Network->WS check the Websocket connection

![](https://cdn-images-1.medium.com/max/800/0*tdQUTsHcGYqRVgw0)
undefined

2) Burp Proxy (Capture the Request and check for Websocket History )

![](https://cdn-images-1.medium.com/max/800/0*R5tBaInGoVd2iEg6)
undefined

**Steps to Identify Cross Site Websocket Hijacking Vulnerability**

Cross-site-websocket-hijacking we can also call it as Websocket CORS vulnerability

To understand this vulnerability we need first look at the websocket upgrade request

The typical lifecycle of a WebSocket interaction between client & server goes as follows:

1.  Client initiates a connection by sending an HTTP(S) WebSocket handshake request.
2.  Server replies with a handshake response (all handled by the web application server transparently to the application) and sends a response status code of _101 Switching Protocols_.

![](https://cdn-images-1.medium.com/max/800/0*JONnBsyotnuh1Otv)
undefined

As you can see from the request headers of the HTTP(S) handshake request, the authentication data Cookies is sent along with the upgrade handshake. The same would be true for HTTP-Authentication data.

Another interesting observation is the **Origin** header that is sent along the WebSocket handshake/upgrade request. This is like in a regular CORS request utilizing _Cross-Origin Resource Sharing_:

Steps to Verify the CSWH vulnerability

1.  Identify the URL using Burp Proxy (wss://host.com)
2.  Now go to [http://www.websocket.org/echo.html](http://www.websocket.org/echo.html) and Enter the web socket URL

![](https://cdn-images-1.medium.com/max/800/0*6JAz5BZrlLD-IbQ-)
undefined

1.  Click connect websocket connection will be established with Origin: [https://www.websocket.org](https://www.websocket.org) 
2.  If the websocket is not connected then application will not be vulnerable to CORS

**How to Perform Manual Testing Using Websocket**

To perform Manual security testing on websocket frames we need to capture all websocket request using burp suite as show below

![](https://cdn-images-1.medium.com/max/800/0*Ycen7ygfcMXlw8zm)
undefined

2)Now List out all frames in Text File and Build business logic according to the application and you can test the vulnerabilities Like IDOR by Manipulating Frames value, XSS, Injection etc

3) I have created the small NodeJs Script to Pentest the websocket manually in below URL

[https://github.com/Shapa7276/Websocket-Pentest/blob/master/WSPentest.js](https://github.com/Shapa7276/Websocket-Pentest/blob/master/WSPentest.js)

Steps to Use above tool

I will demonstrate above scripts on **wss://echo.websocket.org** with **Origin:** [**www.websocket.org**](http://www.websocket.org) and will send **Hello** Frame and

1.  Modify the script with above values in scripts as shown in below Image

![](https://cdn-images-1.medium.com/max/800/0*5Sqf2NTVPzSLq990)
undefined

2)Now Run the Script with command

➜ node WSPentest.js

3) Once your client is connected send “Test” outgoing frame you will receive same output as shown below

![](https://cdn-images-1.medium.com/max/800/0*WAlZ0SvLsUasNKXb)
undefined

4) Using above scripts we can perform Below List of Test Case by modifying it as per requirement

1)Brute Force the any Frame or Request

2)Can be use as Intruder to to Find Idor or different type of parameter Fuzzing

3)We can also verify site is vulnerable to Cross-site websocket Hijacking or not

4)Websocket DOS attacks etc.
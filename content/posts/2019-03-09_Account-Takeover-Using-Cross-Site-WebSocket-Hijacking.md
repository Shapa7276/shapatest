---
title: Account Takeover Using Cross-Site WebSocket Hijacking (CSWH)
description: "Hello\_,"
date: '2019-03-09T14:59:59.234Z'
categories: []
keywords: []
slug: >-
  /@sharan-panegav/account-takeover-using-cross-site-websocket-hijacking-cswh-99cf9cea6c50
---

Hello ,

While Hunting on a private program. I found the application using WebSocket connection so I checked the WebSocket URL and I found it was vulnerable to CSWH(Cross-site websocket-hijacking)

for more details about CSWH you can go through below blog

1.  [https://www.christian-schneider.net/CrossSiteWebSocketHijacking.html](https://www.christian-schneider.net/CrossSiteWebSocketHijacking.html)

So let’s assume an application is an establishing connection with websocket on URL wss://website.com. to verify the URL is vulnerable to CSWH I follow below steps

1.  Open the web application on browser and login into it.
2.  After this visit, [http://websocket.org/echo.html](http://websocket.org/echo.html) in a new tab, enter the WebSocket URL and click ‘Connect’.
3.  Once the connection is established you must be able to send frames to the server from this page. Capture the websocket frames using burp proxy from a valid session and send them to see how the server responds. If the server responds in the same way as it did for the valid session then it most likely is vulnerable to Cross-Site WebSocket Hijacking

![](https://cdn-images-1.medium.com/max/800/1*RAM_H3sV7mJrZOu6rSsdEg.png)
undefined

By following above steps I determined the application is vulnerable to Cross-site-websocket-Hijacking.

Once I established the WebSocket connection on the new tab I have received below websocket response

![](https://cdn-images-1.medium.com/max/800/1*7K_4OWJA-Qx2-7m5M1nTZw.png)
undefined

If you observe the above response, there is parameter “forgotPasswordId” and its value is “null”.

Now need to determine the value of “\_forgotPasswordId” to complete the attack I decided to check the forgot password page and submitted the password reset request.

![](https://cdn-images-1.medium.com/max/800/1*AKYlPYgm7HcdbyD3qyVa5w.png)
undefined

Once again I checked the Websocket connection and this time observed the below Response and it contains forgotPassword token

![](https://cdn-images-1.medium.com/max/800/1*XZRyefocwP4bjNv2-GkAMg.png)
undefined

Exploit :

Now to prepare the exploit of account takeover need to chain CSWH and password reset request. So I prepared below payload to send WebSocket response the attacker site using XHR.

undefined
undefined

Steps:

1.  Send Password reset link to Victim (Using Forgot password page)
2.  Host the Above CSWH.html and Send URL to Vitim (Similar to CSRF attacks)
3.  Once victim click on URL you will get websocket response on your listener as show in below Image

![](https://cdn-images-1.medium.com/max/800/1*Q9ScYIEVOlOKfz13-KrCow.png)

Once we have forgot password token we can reset the victim password
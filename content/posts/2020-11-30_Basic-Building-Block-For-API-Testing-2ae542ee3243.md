---
title: Basic Building Block For API Testing
description: >-
  This blog all about API pentesting. We are dividing this blog in three
  modules:
date: '2018-11-30T18:44:28.057Z'
categories: []
keywords: []
slug: /@sharan-panegav/basic-building-block-for-api-testing-2ae542ee3243
---

This blog all about API pentesting. We are dividing this blog in three modules:

1.  What is API pentesting?
2.  Structure of API request and response?
3.  Methodology, Tools and Test Case to perform Pen testing?

**1) API Pentesting**

Most of the people think that api pentesting is different. This blog tries to explain the basic building blocks of the api pentesting which is quite easy than the regular web app pentesting.

API Pen testing is very identical to web application vulnerability assessment.where methods of these type testing remains similar to other web applications with some small changes in the attack hence we need to look for some standard vulnerabilities that we look for the web application such as Injection, Access Control,information disclosure, IDOR XSS,etc

1.  **API authentication and session management**

Most of the API are protected with authorization tokens in headers which mostly mitigate CORS, CSRF vulnerabilities

1.  Basic Auth

![](https://cdn-images-1.medium.com/max/800/0*4JSe1Pi0J9oDpRXi)
undefined

1.  Authorization Bearer

![](https://cdn-images-1.medium.com/max/800/0*0Pt5NP8sBO07k9_3)
undefined

**2) Structure of API request and response**

As you can see in below Images API uses of HTTP methods (GET, POST, PUT

and DELETE) as the primary verb for the their operations which we can use for VERB tampering

GET: The resource has been fetched and is transmitted in the message body.

HEAD: The entity headers are in the message body.

PUT or POST: The resource describing the result of the action is transmitted in the message body.

**HTTP GET request**

![](https://cdn-images-1.medium.com/max/800/0*IJwmCkTcEvs1wrux)
undefined

**HTTP POST request**

![](https://cdn-images-1.medium.com/max/800/0*MlZKetnn2SRW7XIc)
undefined

**HTTP Response**

**Most of the API uses the JSON standards for receiving data formats:**

1\. JSON API covers how a client should request that resources be fetched or modified

{ “userId”: 1,”rollnumber”: 1,”title”: “name”, “status”: false}

2\. JSend -Simple JSON format.

Below Is Example

{

status : “success”,data : {“post” : { “id” : 1, “title” : “API blog”, “body” : “All data” }}

}

**API Response status**

200 OK

201 Created

301 Moved Permanently

302 Found

400 Bad Request

403 Forbidden

404 Not Found

405 Method Not Allowed

408 Request Timeout

500 Internal Server Error

502 Bad Gateway

**3) Tools used for Pentesting**

Below are the tools can be used to perform API pentesting:

1) Swagger-Editor

2) Postman

3) Burpsuite

**Steps to Import JSON file in Swagger:**

1) Swagar can be downloaded from below URL

2) [https://github.com/swagger-api/swagger-editor/releases](https://github.com/swagger-api/swagger-editor/releases)

Swagger Editor can import your OpenAPI document, which can be formatted as JSON or YAML.

**File → Import File**

Click Choose File and select import. The file you are importing has to be a valid JSON or YAML OpenAPI document. Swagger Editor will prompt you abouhttps://github.com/swagger-api/swagger-editor/releases t validation errors, if any exist.http://blog.securelayer7.net/web-services-api-penetration-testing-part-2/

**File → Import URL**

Paste the URL to your OpenAPI document.

Drag and Drop

Simply drag and drop your OpenAPI JSON or YAML document into the Swagger Editor browser window.

![](https://cdn-images-1.medium.com/max/800/0*8FzeZE0tUVxs2p9j)
undefined

Similarly We can Import Json file using Postman

**Import → Import File**

![](https://cdn-images-1.medium.com/max/800/0*IrMCfoSkqGVQsD8O)
undefined

Once you Import the file you will get all API request in collection tab

![](https://cdn-images-1.medium.com/max/800/0*R0zsAeOO3DlWk7BJ)
undefined

Once we have the API request we can ADD authorization header and Post parameter in Postman

![](https://cdn-images-1.medium.com/max/800/0*P5NOh0SzC8kgOQFR)
undefined

Once we have complete information of Target, request and response then we can modify the the Host in Postman and Swagger and we can proceed for pentesting

**PHASES OF ATTACK:**

**Enumeration and Information Gathering**

Determine the attack surface using documentation provided by the client or the customer. A developer guide can give us more details of inside of API. If documentation is not provided or API is deployed in running serve then we need to capture all API requests using proxy. Identify the POST/GET methods thoroughly to each and every module.

Analyze the all information got from above steps, then follow below basic test cases:

1) Observe each parameter in every module how the data is transformed from one source to another source.

2) Identify if the API has any authorization token, if it is having then remove that authorization token and see application response.

3) Analyze and check each module with different access level of user ex:admin, moderator, normal user.

4) Check whether admin modules can be accessed via the normal user.

5) Identify the parameters which may vulnerable to IDOR type vulnerabilities such as id=1234.

6) Check injection vulnerabilities by inserting special characters in all parameters in request and check response any SQL related errors are raise.

7) Insert greater than, less than (<,>) characters in all parameters and see response whether the application encoding them as > and <. If application don’t escape any special characters then the application may be vulnerable to client-side attacks such as XSS (cross site scripting).

8) Modify the content type from application/json to application/xml insert special characters in all parameters see whether the XML injection can possible or not.

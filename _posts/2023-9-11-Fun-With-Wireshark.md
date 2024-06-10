---
title: Wireshark Unleashed:Popular Tricks and Insights
author: Jason
date: 2023-9-16
category: Jekyll
layout: post

---

## 1. Tracking Traffic
Wireshark offers a feature called "statistics," which aids in honing in on specific conversations or endpoints.
It's found on the navigation bar. For this task, we will select 'conversation'.
![Tracking_traffic](/assets/pics/wireshark/tracking_traffic.png)
In the image below, it's clear that the highlighted row represents the most active conversation.
![Tracking_traffic](/assets/pics/wireshark/tracking_traffic2.png)
Right-click on the conversation and choose "Apply a->b as Filter > Selected". In the main Wireshark window, you can then determine the total packets and bytes from a->b, and vice versa for b->a.
![Tracking_traffic](/assets/pics/wireshark/tracking_traffic3.png)


## 2. Detect HTTP
I will first use a filter to display the HTTP response time for each HTTP request.

```
http.time
```
To analyze and inspect these packets and data, I'll introduce a custom column at the beginning of the panel.
Right-click on the top of the panel and choose "Column Preferences". In the pop-up window, click '+'. In the field, enter "http.time". For the type, select "Custom", and for the title, input "HTTP Response Time".
![Detect HTTP](/assets/pics/wireshark/Detect_HTTP1.png)
Next, I will introduce several HTTP status codes and explain their meanings.
### 2.1 HTTP Response CODE
HTTP response status codes are categorized into five classes: 
* 1xx indicates informational responses, suggesting the request has been received and is under processing; 
* 2xx indicates success, meaning the request was successfully received, understood, and accepted; 
* 3xx points to redirection, implying that further action is required by the client to complete the request; 
* 4xx represents client errors, suggesting that there seems to be a mistake made by the client; 
* 5xx signifies server errors, indicating the server failed to fulfill a valid request.

## 3. FTP Fundamentals
To display FTP request and response packets in Wireshark, use the following filter:
```
ftp
```
If you specifically want to filter for FTP requests only, you might use filters like ftp.request. Conversely, for responses, a filter such as ftp.response might be applicable.
### 3.1 FTP Respnse CODE
FTP (File Transfer Protocol) utilizes three-digit response codes to signify the status of client requests. 
* Preliminary replies (1xx) suggest an action started but await another reply, such as 150 indicating the file status is okay. 
* Completion replies (2xx) confirm a successful action, exemplified by 200 meaning "Command okay." 
* Intermediate replies (3xx) denote more information is needed, like 331 which means "User name okay, need password." 
* Transient negative completion replies (4xx) indicate a temporary rejection, with 421 suggesting the service isn't available. 
* Permanent negative completion replies (5xx) represent definitive action failure, with codes like 550 showing the requested action wasn't taken due to file unavailability.

## 4. DHCP Decoded
* Layer of the OSI model:
DHCP Discover packets can be found at the Application Layer (Layer 7) of the OSI model. However, when discussing protocols in terms of the OSI model, it's important to note that DHCP primarily operates at the Application Layer, but it also uses functionalities of the Transport Layer and Network Layer for its operations.
* Type of packet: 
DHCP Discover is a broadcast packet. It's the initial message sent by a client when it attempts to join a network and seeks to get IP configuration parameters, including an IP address from a DHCP server.

* Source and Destination IP Addresses:
  * Source IP Address:** 0.0.0.0 (Because the client doesn't have an assigned IP address yet.)
  * Destination IP Address:** 255.255.255.255 (This is a broadcast address, meaning the Discover packet is intended for all devices on the network.)
  * Port Numbers:
    * Source Port:68 (DHCP clients use this port to communicate with servers.)
    * Destination Port:67 (DHCP servers listen on this port for client requests.)
    The DHCP Discover packet is the first step in the DHCP four-step process (Discover, Offer, Request, and Acknowledge) used to obtain network configuration parameters from the DHCP server. 

## 5. DNS Demystified
DNS (Domain Name System) queries primarily use the UDP (User Datagram Protocol) as their transport layer protocol. Specifically, DNS queries typically use UDP on port 53. However, it's worth noting that DNS can also use TCP (Transmission Control Protocol) for tasks that require reliable delivery, such as zone transfers or when the response data size exceeds 512 bytes and cannot be fit into a single UDP packet. In those cases, DNS also uses TCP on port 53.
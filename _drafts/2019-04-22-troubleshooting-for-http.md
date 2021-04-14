---
layout: post
title:  Troubleshooting for Http
date:   2019-04-22 23:16:00 +0800
categories: troubleshooting
---

>

## 1. Status Code[1]

### 302 Found
A common way of performing URL redirection.

### 400 Bad Request
Cause
+ the HTTP request that was sent to the server has invalid syntax. 
+ the user's cookie that is associated with the site is corrupt.

Checkage
+ clearing the browser's cache and cookies could solve this issue
+ malformed request due to a faulty browser
+ malformed request due to human error when manually forming HTTP requests (e.g. using curl incorrectly)

### 401  Unauthorized Error
Client-side checkage
+ check if inputting an incorrect URL
+ clear cookies to update auth info
+ clear cache to refresh webpage
+ logout and login

Server-side checkage
+ confirm the WWW-Authenticate Header
+ check Web Server Configuration

### 403 Forbidden
Cause
+ the server understood the request, but will not fulfill it due to client related issues.

### 404 Not Found
Checkage
+ nginx try_files settings

### 451 Unavailable For Legal Reasons
Cause
+ the webpage censored by a government

### 500 Internal Server Error
Checkage
+ first check nginx's error.log
+ then php's php_errors.log
+ then the programming  

### 502 Bad Gateway
Cause
+ the server is a gateway or proxy server, and it is not receiving a valid response from the backend servers that should actually fulfill the request. 
 
If the server in question is a reverse proxy server, such as a load balancer, here are a few checkage
+ the backend servers (where the HTTP requests are being forwarded to) are healthy
+ the reverse proxy is configured properly, with the proper backends specified
+ the network connection between the backend servers and reverse proxy server is healthy. If the servers can communicate on other ports, make sure that the firewall is allowing the traffic between them
+ if your web application is configured to listen on a socket, ensure that the socket exists in the correct location and that it has the proper permissions

### 503 Service Unavailable
The server is not ready to handle the request.

Cause
+ a server being down for maintenance or being overloaded

Caution
Caching-related headers that are sent along with this response should be taken care of, as a 503 status is often a temporary condition and responses shouldn't usually be cached.

### 504 Gateway Timeout
A different computer that doesn't control but relies on, isn't communicating with quickly enough.


## A. Reference
1. [How To Troubleshoot Common HTTP Error Codes](https://www.digitalocean.com/community/tutorials/how-to-troubleshoot-common-http-error-codes)

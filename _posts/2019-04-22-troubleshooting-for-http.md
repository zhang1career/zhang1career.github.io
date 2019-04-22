---
layout: post
title:  Troubleshooting for Http Status Code
date:   2019-04-22 23:16:00 +0800
categories: troubleshooting
---

>

# Status Code

## 3XX

### 302 Found
A common way of performing URL redirection.

## 4XX

### 400 Bad Request
The HTTP request that was sent to the server has invalid syntax. 
The user's cookie that is associated with the site is corrupt.
Clearing the browser's cache and cookies could solve this issue
Malformed request due to a faulty browser
Malformed request due to human error when manually forming HTTP requests (e.g. using curl incorrectly)

### 401  Unauthorized Error
Client-side checkage:
Check if inputting an incorrect URL
Clear cookies to update auth info
Clear cache to refresh webpage
Logout and Login
Server-side checkage:
Confirm the WWW-Authenticate Header
Check Web Server Configuration

### 403 Forbidden
The server understood the request, but will not fulfill it due to client related issues.

### 404 Not Found
nginx try_files 引导至此

### 451 Unavailable For Legal Reasons
Such as the webpage censored by a government.

## 5XX

### 500 Internal Server Error
nginx try_files 未定义最有1个参数

### 502 Bad Gateway
The server is a gateway or proxy server, and it is not receiving a valid response from the backend servers that should actually fulfill the request. 
 
If the server in question is a reverse proxy server, such as a load balancer, here are a few things to check:

The backend servers (where the HTTP requests are being forwarded to) are healthy
The reverse proxy is configured properly, with the proper backends specified
The network connection between the backend servers and reverse proxy server is healthy. If the servers can communicate on other ports, make sure that the firewall is allowing the traffic between them
If your web application is configured to listen on a socket, ensure that the socket exists in the correct location and that it has the proper permissions

### 503 Service Unavailable
This response code indicates that the server is not ready to handle the request.

Common causes are a server that is down for maintenance or that is overloaded. This response should be used for temporary conditions and the Retry-After HTTP header should, if possible, contain the estimated time for the recovery of the service.
 
Caching-related headers that are sent along with this response should be taken care of, as a 503 status is often a temporary condition and responses shouldn't usually be cached.

### 504 Gateway Timeout
Usually indicate that a different computer, one that doesn't control but relies on, isn't communicating with quickly enough.

https://www.digitalocean.com/community/tutorials/how-to-troubleshoot-common-http-error-codes

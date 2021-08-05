---
title: "HTTP IO"
date: 2021-08-04T08:54:59+02:00
draft: true
weight: 2
---

## Client-side
### Timeouts
HTTP clients should always have a 1) connect timeout and 2) a read timeout. For most cases the total time between these two should be around 1 second.

### Retries
A remote service could be down for a bit or maybe the network is unreliable. Either way retrying a failed call is usually a good idea.

Retries should only be performed for certain error responses. 
- Usually **2xx** http response codes mean everything is fine.  
- **4xx** mean that we were in error. The error was on the caller side.
- **5xx** responses could be an indicator that we can retry the call. 

Check that the remote endpoint allows for retries. Some servers have their own internal retry policy and expect you 
not to handle retries your self. **Make sure retrying is supported** by the remote service provider. 


A **short delay** between retries can help. Some have even increased the time between subsequent retries. 
This can create another issue if someone calling your service is going to timeout.

There should be a **max number of retries**. This could be global to the microservice or organization. 
It could then be overridden for individual services.

## Server-side
### Timeouts

Hosted services should always have a timeout. This is important in a number of ways.
- It protects the service from having many slow clients connected.
- It ensures connections don't stay open forever.
- It provides a deadline for the service to work within. If this service can't meet the deadline it should be async.

### Response codes
Provide correct HTTP response codes on calls. 

### Versioning
APIs and services should have version numbers. For example **/api/customer/v1**. 
When the body of the request changes or the responsibility of the service you should publish a new version. 


Support old versions for a time but do not support too many at once. Deprecate old versions and let consumers.
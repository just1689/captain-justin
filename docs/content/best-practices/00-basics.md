---
title: "Basics"
date: 2021-08-04T08:54:59+02:00
draft: true
weight: 1
---

## State
Microservices should default to not holding state in memory. 
The theory is that the source of truth sits outside the microservice &amp; that the microservices can be scaled-out to **n** replicas.
  

## Sessions
Sessions are a great example of state that could end-up being saved in memory. Sessions must not be kept in memory in a microservice.
Ideally they are managed by another layer like an API gateway / service mesh. 
If the microservice has to concern itself with the session then it should check against an external stateful store. Redis for example.

## Logging
Logging is essential for supporting an application. Logs can be used to solve different problems. This must be resolved.
A consistent logging philosophy and approach should be used throughout the team or area.

Some have used a log as a datastore. That is probably not a good idea. CQRS might be a better fit for an **always-append** approach to state.

Some have logs as the official record for business events. This might sound like a good idea, 
but when mixed in with other random logs, the logs might have a retention policy that doesn't fit well for a subset of the logs. 
You may keep business audit logs for far too short of a time (because all the noisy logs take too much space).
You may keep system logs for far too long to meet an audit retention requirement for business logs.

It is worth considering having a separate database-persisted audit of record. That way logs can be used for system events only.

### Logging Levels
[coming soon]

### Fields
Log lines should also include the following fields
- Correlation ID for the request or event.
- The Git revision of the code that was running.
- Timestamp
- The IP of the machine it is running on.

If you have the correlation ID you can follow the life of a request in a distributed system.
If you have the Git revision you know what code was running that produced that log line.
Timestamp is obviously useful. The IP of the machine have help debug containerized workloads. 

## Metrics
Metrics should be collected from all running applications. 
Some of these work out of the box using Spring Actuator with Micrometer for example.

The team should look at the metrics being consumed and ask whether there are custom metrics worth exposing.
These custom metrics could be useful in supporting the application &amp; providing a high quality of service to customers.

Not only should the team ensure their application exposes metrics but that
- the metrics are consumed by Prometheus or similar
- rendered or visualized in someway where useful
- alarms are created against relevant high-water marks where a line is crossed for some period of time.

## Tracing
Applications should ensure tracing is used. These can be pushed or made available for scraping to a store for traces.
Jaeger is one example of an application for handling tracing concerns.

### Between microservices
Generally speaking, it is difficult to support distributed solutions. One aspect of this is tracing the lifetime of an 
incoming service request as it is passed around between different microservices within the environment. 
A microservice could be experiencing downtime. It may have intermittent or degraded service. 
It may appear up when the support team investigates but have been unavailable when another microservice called.

For this &amp; other reasons we should always log **traces** whenever one microservice calls out to another. 
The microservices themselves should also log using the correlation ID (see above section).

This would help establish that **Service A** was able to connect to **Service B**. The trace would also show how long 
was spent on the call which can help in investigating issues related to response time.


### Between components
Many find it advantageous to trace calls between components internal to a microservice, especially where they have distinct responsibilities.
Externalizing the traces along with their times can help in monitoring for big changes in time taken internally.
This in turn allows teams to be proactive, identifying inefficiencies as before they become issues.  

## Health Checks
Applications should have a health check endpoint.
- The health check must be make available over HTTP (or as a command).
- The health check should take application internals into account (connection to the DB for example).
- The health check must be consumed.
- Alerts &amp; dashboards should be wired to the health check.
- Relevant parties should be notified for the alarm.
- Some health checks are only an issue if the alarm stays on.

## Pre-stop hooks
Where possible an application should be removed from the load balancer before being sent the **SIGTERM** signal.

### Automatic restarts
It is worth considering if an application should automatically restart if it suffers a fatal error.
This is the norm in containerized applications. It may not be achievable when running in traditional legacy JVM servers.

## Magic variables



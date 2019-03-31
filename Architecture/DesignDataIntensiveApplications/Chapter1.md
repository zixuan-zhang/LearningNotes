# Chapter 1 Reliable, Scalable, and Maintainable Applications

## Describing Performance
>>>Latency and response
Latency and response time are not the same. The response time is what the client see: besides the actual time to process the request(the service time), it includes network delays and queueing delays. Latency is the duration that a request is waiting to be handled-during which it is latent, awaiting service.

It's common to see average response time of a service reported(arithmetic mean). However, the mean is not a very good metrics, because it doesn't tell you how many users actually experienced that delay.

Usually it's better to user **percentiles**. Median is a good metrics if you want to know how long the users typically have to wait. The median is also know as the 50th percentile, abbreviated as p50.

Percentiles are often used in service level objective(SLOs) and service level agreements(SLAs), contracts that define the expected performance and availability of a service. An SLA may state that the service is considered to be up if it has a median response time of less than 200ms and a 99th percentile under 1s, and the service may be required to be up at least 99.9% of the time; These metrics set expectations for clients of the service and allow customers to demand a refund if the SLA is not met.

## Maintainability
* Operability: Make it easy for operations teams to keep the system running smoothly
* Simplicity: Make it easy for new engineers to understand the system, by removing as much complexity as possible from the system.
* Evolvability: Make it easy for engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change. Also known as extensibility, modifiability or plasticity.

A good operations team typically is responsible for the following:
* Monitoring the health of the system and quickly restoring service if it goes into a bad state
* Tracking down the cause of problems, such as system failures or degraded performance
* Keeping software and platforms up to date, including security patches
* Keeping tabs on how different systems affect each other, so that a problematic change can be avoided before it causes damage
* Anticipating future problems and solving them before they occur(e.g., capacity planning)
* Establishing good practices and tools for deployment, configuration management
* Performing complex maintenance tasks, such as moving an application from one platform to another
* Maintaining the security of the system as configuration changes are made
* Defining processes that make operations predictable and help keep the production environment stable
* Preserving the organizations's knowledge about the system, even as individual people come and go

Data systems can do various things to make routine tasks easy, including:
* Providing visibility into the runtime behavior and internals of the system, with good monitoring
* Providing good support for automation and integration with standard tools
* Avoiding dependency on individual machines
* Providing good documentation and an easy-to-understand operational model
* Providing good default behavior, but also giving administrators the freedom to override defaults when needed
* Self-healing where appropriate, but also giving administrators manual control over the system state when needed
* Exhibiting predictable behavior, minimizing surprises


### Simplicity
One of the best tools we have for removing accidental complexity is abstraction. A good abstraction can hide a great deal of implementation detail behind a clean simple-to-understand facade.

## Summary
Reliability means making systems work correctly, even when faults occur.
Scalability means having strategies for keeping performance good, even when load increases.

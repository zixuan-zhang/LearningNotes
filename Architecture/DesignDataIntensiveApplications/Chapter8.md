
# Chapter 8 The trouble with Distributed Systems

If we want to make distributed system work, we must accept the possibility of partial failure and build fault-tolerance mechanisms into the software. In other words, we need to build a reliable system from unreliable components.


## Faults and Parial Failures

## Unreliable Networks
Use some cases to illustrate that the network is unreliable.

### Network Faults and Practice
Handling network faults doesn’t necessarily mean tolerating them: if your network is normally fairly reliable, a valid approach may be to simply show an error message to users while your network is experiencing problems. However, you do need to know how your software reacts to network problems and ensure that the system can recover from them. It may make sense to deliberately trigger network problems and test the system’s response.

### Detecting Faults
Many systems need to automatically detect faulty nodes. For example:
• A load balancer needs to stop sending requests to a node that is dead (i.e., take it
out of rotation).
• In a distributed database with single-leader replication, if the leader fails, one of the followers needs to be promoted to be the new leader

Rapid feedback about a remote node being down is useful, but you can’t count on it. Even if TCP acknowledges that a packet was delivered, the application may have crashed before handling it. If you want to be sure that a request was successful, you need a positive response from the application itself

### Timeouts and Unbounded Delays
Even better, rather than using configured constant timeouts, systems can continually measure response times and their variability (jitter), and automatically adjust time‐ outs according to the observed response time distribution. This can be done with a Phi Accrual failure detector [30], which is used for example in Akka and Cassandra [31]. TCP retransmission timeouts also work similarly [27].
According to historical data.

### Synchronous Versus Asynchronous Networks
Distributed systems would be a lot simpler if we could rely on the network to deliver packets with some fixed maximum delay, and not to drop packets. Why can’t we solve this at the hardware level and make the network reliable so that the software doesn’t need to worry about it?

When you make a call over the telephone network, it establishes a circuit: a fixed, guaranteed amount of bandwidth is allocated for the call, along the entire route between the two callers. This circuit remains in place until the call ends [32]. For example, an ISDN network runs at a fixed rate of 4,000 frames per second. When a call is established, it is allocated 16 bits of space within each frame (in each direction). Thus, for the duration of the call, each side is guaranteed to be able to send exactly 16 bits of audio data every 250 microseconds [33, 34].
 284 | Chapter 8: The Trouble with Distributed Systems
This kind of network is synchronous: even as data passes through several routers, it does not suffer from queueing, because the 16 bits of space for the call have already been reserved in the next hop of the network. And because there is no queueing, the maximum end-to-end latency of the network is fixed. We call this a bounded delay.

that a circuit in a telephone network is very different from a TCP connection: a circuit is a fixed amount of reserved bandwidth which nobody else can use while the circuit is established, whereas the packets of a TCP connection opportunistically use whatever network bandwidth is available

Why do datacenter networks and the internet use packet switching? The answer is that they are optimized for bursty traffic. The circuit may have low utilization. So it's a trade off of latency and resource utilization.

## Unreliable Clocks

### Monotonic Versus Time-of-Day Clocks
* Monotonic clock is monotonic and used for measuring eclapse time.
* Time-of-Day clock is used for getting current time and the time can be adjusted and my move forward or backward. 


### Clock Synchronization and Accuracy

### Relying on Synchronized Clocks

### Process Pauses

## Knowledge, Truth and Lies

### The Truth is Defined by the Majority

### Byzantine Faults

### System Model and Reality

## Summary

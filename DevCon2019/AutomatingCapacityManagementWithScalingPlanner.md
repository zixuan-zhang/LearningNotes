# Scaling Planner

## Agenda
* Pain points of manual capacity management and scaling
* Scaling Planner
* Aggregate Pool
* Peak Event Planning using Scaling Planner
* What's new?

## Scaling Planner
* Aim at increase the CPU utilizations in down period.

## Setting Scaling Planner
* Capacity lower bound
* mean time to traffic? Predict the peak, need some time before traffic.
* MAX
* Desired capacity
* MIN
* Balancing groups

## How does it all work
Predictive Scaling -> Predict forecast & Schedule scaling actions
* AWS Auto scaling -> Reactive scaling with CPU tar

## Safety in scaling Planner
* Reactive scaling ?
* Predictive scaling ?

Do we own the hosts?
I doubt the performance of scaling.

## Max Management: Bursting
Only billed for those instance you actually run.

## Current State of Capacity planning for peaks and organic
* Manually creating orders for the expected peak event
* Manully descaling the fleet

I doubt the performance of reactive and predictive scaling. 

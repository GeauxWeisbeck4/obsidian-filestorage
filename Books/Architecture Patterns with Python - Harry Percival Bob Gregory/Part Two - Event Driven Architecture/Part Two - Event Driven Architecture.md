---
tags: MOCs
---
```folder-index-content
```
# Part II. Event-Driven Architecture

> I’m sorry that I long ago coined the term “objects” for this topic because it gets many people to focus on the lesser idea.
> 
> The big idea is “messaging."…The key in making great and growable systems is much more to design how its modules communicate rather than what their internal properties and behaviors should be.
> 
> Alan Kay

It’s all very well being able to write _one_ domain model to manage a single bit of business process, but what happens when we need to write _many_ models? In the real world, our applications sit within an organization and need to exchange information with other parts of the system. You may remember our context diagram shown in [Figure II-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/part02.html#allocation_context_diagram_again).

Faced with this requirement, many teams reach for microservices integrated via HTTP APIs. But if they’re not careful, they’ll end up producing the most chaotic mess of all: the distributed big ball of mud.

In Part II, we’ll show how the techniques from [Part I](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/part01.html#part1) can be extended to distributed systems. We’ll zoom out to look at how we can compose a system from many small components that interact through asynchronous message passing.

We’ll see how our Service Layer and Unit of Work patterns allow us to reconfigure our app to run as an asynchronous message processor, and how event-driven systems help us to decouple aggregates and applications from one another.

![apwp 0102](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0102.png)

###### Figure II-1. But exactly how will all these systems talk to each other?

We’ll look at the following patterns and techniques:

Domain Events

Trigger workflows that cross consistency boundaries.

Message Bus

Provide a unified way of invoking use cases from any endpoint.

CQRS

Separating reads and writes avoids awkward compromises in an event-driven architecture and enables performance and scalability improvements.

Plus, we’ll add a dependency injection framework. This has nothing to do with event-driven architecture per se, but it tidies up an awful lot of loose ends.
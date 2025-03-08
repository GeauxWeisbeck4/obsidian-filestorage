# Chapter 9. Going to Town on the Message Bus

In this chapter, we’ll start to make events more fundamental to the internal structure of our application. We’ll move from the current state in [Figure 9-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#maps_chapter_08_before), where events are an optional side effect…

![apwp 0901](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0901.png)

###### Figure 9-1. Before: the message bus is an optional add-on

…to the situation in [Figure 9-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#map_chapter_08_after), where everything goes via the message bus, and our app has been transformed fundamentally into a message processor.

![apwp 0902](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0902.png)

###### Figure 9-2. The message bus is now the main entrypoint to the service layer

###### TIP

The code for this chapter is in the chapter_09_all_messagebus branch [on GitHub](https://oreil.ly/oKNkn):

git clone https://github.com/cosmicpython/code.git
cd code
git checkout chapter_09_all_messagebus
# or to code along, checkout the previous chapter:
git checkout chapter_08_events_and_message_bus

# A New Requirement Leads Us to a New Architecture

Rich Hickey talks about _situated software,_ meaning software that runs for extended periods of time, managing a real-world process. Examples include warehouse-management systems, logistics schedulers, and payroll systems.

This software is tricky to write because unexpected things happen all the time in the real world of physical objects and unreliable humans. For example:

- During a stock-take, we discover that three `SPRINGY-MATTRESS`es have been water damaged by a leaky roof.
    
- A consignment of `RELIABLE-FORK`s is missing the required documentation and is held in customs for several weeks. Three `RELIABLE-FORK`s subsequently fail safety testing and are destroyed.
    
- A global shortage of sequins means we’re unable to manufacture our next batch of `SPARKLY-BOOKCASE`.
    

In these types of situations, we learn about the need to change batch quantities when they’re already in the system. Perhaps someone made a mistake on the number in the manifest, or perhaps some sofas fell off a truck. Following a conversation with the business,[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#idm45846299214296) we model the situation as in [Figure 9-3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#batch_changed_events_flow_diagram).

![apwp 0903](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0903.png)

###### Figure 9-3. Batch quantity changed means deallocate and reallocate

An event we’ll call `BatchQuantityChanged` should lead us to change the quantity on the batch, yes, but also to apply a _business rule_: if the new quantity drops to less than the total already allocated, we need to _deallocate_ those orders from that batch. Then each one will require a new allocation, which we can capture as an event called `AllocationRequired`.

Perhaps you’re already anticipating that our internal message bus and events can help implement this requirement. We could define a service called `change_batch_quantity` that knows how to adjust batch quantities and also how to _deallocate_ any excess order lines, and then each deallocation can emit an `AllocationRequired` event that can be forwarded to the existing `allocate` service, in separate transactions. Once again, our message bus helps us to enforce the single responsibility principle, and it allows us to make choices about transactions and data integrity.

## Imagining an Architecture Change: Everything Will Be an Event Handler

But before we jump in, think about where we’re headed. There are two kinds of flows through our system:

- API calls that are handled by a service-layer function
    
- Internal events (which might be raised as a side effect of a service-layer function) and their handlers (which in turn call service-layer functions)
    

Wouldn’t it be easier if everything was an event handler? If we rethink our API calls as capturing events, the service-layer functions can be event handlers too, and we no longer need to make a distinction between internal and external event handlers:

- `services.allocate()` could be the handler for an `AllocationRequired` event and could emit `Allocated` events as its output.
    
- `services.add_batch()` could be the handler for a `BatchCreated` event.[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#idm45846299188664)
    

Our new requirement will fit the same pattern:

- An event called `BatchQuantityChanged` can invoke a handler called `change_batch_quantity()`.
    
- And the new `AllocationRequired` events that it may raise can be passed on to `services.allocate()` too, so there is no conceptual difference between a brand-new allocation coming from the API and a reallocation that’s internally triggered by a deallocation.
    

All sound like a bit much? Let’s work toward it all gradually. We’ll follow the [Preparatory Refactoring](https://oreil.ly/W3RZM) workflow, aka “Make the change easy; then make the easy change”:

1. We refactor our service layer into event handlers. We can get used to the idea of events being the way we describe inputs to the system. In particular, the existing `services.allocate()` function will become the handler for an event called `AllocationRequired`.
    
2. We build an end-to-end test that puts `BatchQuantityChanged` events into the system and looks for `Allocated` events coming out.
    
3. Our implementation will conceptually be very simple: a new handler for `BatchQuantityChanged` events, whose implementation will emit `AllocationRequired` events, which in turn will be handled by the exact same handler for allocations that the API uses.
    

Along the way, we’ll make a small tweak to the message bus and UoW, moving the responsibility for putting new events on the message bus into the message bus itself.

# Refactoring Service Functions to Message Handlers

We start by defining the two events that capture our current API inputs—`AllocationRequired` and `BatchCreated`:

_BatchCreated and AllocationRequired events (src/allocation/domain/events.py)_

```
@dataclass
```

Then we rename _services.py_ to _handlers.py_; we add the existing message handler for `send_out_of_stock_notification`; and most importantly, we change all the handlers so that they have the same inputs, an event and a UoW:

_Handlers and services are the same thing (src/allocation/service_layer/handlers.py)_

```
def
```

The change might be clearer as a diff:

_Changing from services to handlers (src/allocation/service_layer/handlers.py)_

 def add_batch(
`-        ref: str, sku: str, qty: int, eta: Optional[date],`
`-        uow: unit_of_work.AbstractUnitOfWork`
`+        event: events.BatchCreated, uow: unit_of_work.AbstractUnitOfWork`
 ):
     with uow:
`-        product = uow.products.get(sku=sku)`
`+        product = uow.products.get(sku=event.sku)`
     ...


 def allocate(
`-        orderid: str, sku: str, qty: int,`
`-        uow: unit_of_work.AbstractUnitOfWork`
`+        event: events.AllocationRequired, uow: unit_of_work.AbstractUnitOfWork`
 ) -> str:
`-    line = OrderLine(orderid, sku, qty)`
`+    line = OrderLine(event.orderid, event.sku, event.qty)`
     ...

`+`
`+def send_out_of_stock_notification(`
`+        event: events.OutOfStock, uow: unit_of_work.AbstractUnitOfWork,`
`+):`
`+    email.send(`
     ...

Along the way, we’ve made our service-layer’s API more structured and more consistent. It was a scattering of primitives, and now it uses well-defined objects (see the following sidebar).

##### FROM DOMAIN OBJECTS, VIA PRIMITIVE OBSESSION, TO EVENTS AS AN INTERFACE

Some of you may remember [“Fully Decoupling the Service-Layer Tests from the Domain”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#primitive_obsession), in which we changed our service-layer API from being in terms of domain objects to primitives. And now we’re moving back, but to different objects? What gives?

In OO circles, people talk about _primitive obsession_ as an anti-pattern: avoid primitives in public APIs, and instead wrap them with custom value classes, they would say. In the Python world, a lot of people would be quite skeptical of that as a rule of thumb. When mindlessly applied, it’s certainly a recipe for unnecessary complexity. So that’s not what we’re doing per se.

The move from domain objects to primitives bought us a nice bit of decoupling: our client code was no longer coupled directly to the domain, so the service layer could present an API that stays the same even if we decide to make changes to our model, and vice versa.

So have we gone backward? Well, our core domain model objects are still free to vary, but instead we’ve coupled the external world to our event classes. They’re part of the domain too, but the hope is that they vary less often, so they’re a sensible artifact to couple on.

And what have we bought ourselves? Now, when invoking a use case in our application, we no longer need to remember a particular combination of primitives, but just a single event class that represents the input to our application. That’s conceptually quite nice. On top of that, as you’ll see in [Appendix E](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#appendix_validation), those event classes can be a nice place to do some input validation.

## The Message Bus Now Collects Events from the UoW

Our event handlers now need a UoW. In addition, as our message bus becomes more central to our application, it makes sense to put it explicitly in charge of collecting and processing new events. There was a bit of a circular dependency between the UoW and message bus until now, so this will make it one-way:

_Handle takes a UoW and manages a queue (src/allocation/service_layer/messagebus.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO1-1)

The message bus now gets passed the UoW each time it starts up.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO1-2)

When we begin handling our first event, we start a queue.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO1-3)

We pop events from the front of the queue and invoke their handlers (the `HANDLERS` dict hasn’t changed; it still maps event types to handler functions).

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO1-5)

The message bus passes the UoW down to each handler.

[![5](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/5.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO1-6)

After each handler finishes, we collect any new events that have been generated and add them to the queue.

In _unit_of_work.py_, `publish_events()` becomes a less active method, `collect_new_events()`:

_UoW no longer puts events directly on the bus (src/allocation/service_layer/unit_of_work.py)_

```
-from . import messagebus  
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO2-1)

The `unit_of_work` module now no longer depends on `messagebus`.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO2-2)

We no longer `publish_events` automatically on commit. The message bus is keeping track of the event queue instead.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO2-3)

And the UoW no longer actively puts events on the message bus; it just makes them available.

## Our Tests Are All Written in Terms of Events Too

Our tests now operate by creating events and putting them on the message bus, rather than invoking service-layer functions directly:

_Handler tests use events (tests/unit/test_handlers.py)_

class TestAddBatch:

     def test_for_new_product(self):
         uow = FakeUnitOfWork()
`-        services.add_batch("b1", "CRUNCHY-ARMCHAIR", 100, None, uow)`
`+        messagebus.handle(`
`+            events.BatchCreated("b1", "CRUNCHY-ARMCHAIR", 100, None), uow`
`+        )`
         assert uow.products.get("CRUNCHY-ARMCHAIR") is not None
         assert uow.committed

...

 class TestAllocate:

     def test_returns_allocation(self):
         uow = FakeUnitOfWork()
`-        services.add_batch("batch1", "COMPLICATED-LAMP", 100, None, uow)`
`-        result = services.allocate("o1", "COMPLICATED-LAMP", 10, uow)`
`+        messagebus.handle(`
`+            events.BatchCreated("batch1", "COMPLICATED-LAMP", 100, None), uow`
`+        )`
`+        result = messagebus.handle(`
`+            events.AllocationRequired("o1", "COMPLICATED-LAMP", 10), uow`
`+        )`
         assert result == "batch1"

## A Temporary Ugly Hack: The Message Bus Has to Return Results

Our API and our service layer currently want to know the allocated batch reference when they invoke our `allocate()` handler. This means we need to put in a temporary hack on our message bus to let it return events:

_Message bus returns results (src/allocation/service_layer/messagebus.py)_

 def handle(event: events.Event, uow: unit_of_work.AbstractUnitOfWork):
`+    results = []`
     queue = [event]
     while queue:
         event = queue.pop(0)
         for handler in HANDLERS[type(event)]:
`-            handler(event, uow=uow)`
`+            results.append(handler(event, uow=uow))`
             queue.extend(uow.collect_new_events())
`+    return results`

It’s because we’re mixing the read and write responsibilities in our system. We’ll come back to fix this wart in [Chapter 12](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#chapter_12_cqrs).

## Modifying Our API to Work with Events

_Flask changing to message bus as a diff (src/allocation/entrypoints/flask_app.py)_

```
 @app.route("/allocate", methods=['POST'])
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO3-1)

Instead of calling the service layer with a bunch of primitives extracted from the request JSON…

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO3-2)

We instantiate an event.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO3-3)

Then we pass it to the message bus.

And we should be back to a fully functional application, but one that’s now fully event-driven:

- What used to be service-layer functions are now event handlers.
    
- That makes them the same as the functions we invoke for handling internal events raised by our domain model.
    
- We use events as our data structure for capturing inputs to the system, as well as for handing off of internal work packages.
    
- The entire app is now best described as a message processor, or an event processor if you prefer. We’ll talk about the distinction in the [next chapter](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch10.html#chapter_10_commands).
    

# Implementing Our New Requirement

We’re done with our refactoring phase. Let’s see if we really have “made the change easy.” Let’s implement our new requirement, shown in [Figure 9-4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#reallocation_sequence_diagram): we’ll receive as our inputs some new `BatchQuantityChanged` events and pass them to a handler, which in turn might emit some `AllocationRequired` events, and those in turn will go back to our existing handler for reallocation.

![apwp 0904](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0904.png)

###### Figure 9-4. Sequence diagram for reallocation flow

###### WARNING

When you split things out like this across two units of work, you now have two database transactions, so you are opening yourself up to integrity issues: something could happen that means the first transaction completes but the second one does not. You’ll need to think about whether this is acceptable, and whether you need to notice when it happens and do something about it. See [“Footguns”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#footguns) for more discussion.

## Our New Event

The event that tells us a batch quantity has changed is simple; it just needs a batch reference and a new quantity:

_New event (src/allocation/domain/events.py)_

```
@dataclass
```

# Test-Driving a New Handler

Following the lessons learned in [Chapter 4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#chapter_04_service_layer), we can operate in “high gear” and write our unit tests at the highest possible level of abstraction, in terms of events. Here’s what they might look like:

_Handler tests for change_batch_quantity (tests/unit/test_handlers.py)_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO4-1)

The simple case would be trivially easy to implement; we just modify a quantity.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#co_going_to_town_on_the_message_bus_CO4-3)

But if we try to change the quantity to less than has been allocated, we’ll need to deallocate at least one order, and we expect to reallocate it to a new batch.

## Implementation

Our new handler is very simple:

_Handler delegates to model layer (src/allocation/service_layer/handlers.py)_

```
def
```

We realize we’ll need a new query type on our repository:

_A new query type on our repository (src/allocation/adapters/repository.py)_

```
class
```

And on our `FakeRepository` too:

_Updating the fake repo too (tests/unit/test_handlers.py)_

```
class
```

###### NOTE

We’re adding a query to our repository to make this use case easier to implement. So long as our query is returning a single aggregate, we’re not bending any rules. If you find yourself writing complex queries on your repositories, you might want to consider a different design. Methods like `get_most_popular_products` or `find_products_by_order_id` in particular would definitely trigger our spidey sense. [Chapter 11](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch11.html#chapter_11_external_events) and the [epilogue](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#epilogue_1_how_to_get_there_from_here) have some tips on managing complex queries.

## A New Method on the Domain Model

We add the new method to the model, which does the quantity change and deallocation(s) inline and publishes a new event. We also modify the existing allocate function to publish an event:

_Our model evolves to capture the new requirement (src/allocation/domain/model.py)_

```
class
```

We wire up our new handler:

_The message bus grows (src/allocation/service_layer/messagebus.py)_

```
HANDLERS
```

And our new requirement is fully implemented.

# Optionally: Unit Testing Event Handlers in Isolation with a Fake Message Bus

Our main test for the reallocation workflow is _edge-to-edge_ (see the example code in [“Test-Driving a New Handler”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#test-driving-ch9)). It uses the real message bus, and it tests the whole flow, where the `BatchQuantityChanged` event handler triggers deallocation, and emits new `AllocationRequired` events, which in turn are handled by their own handlers. One test covers a chain of multiple events and handlers.

Depending on the complexity of your chain of events, you may decide that you want to test some handlers in isolation from one another. You can do this using a “fake” message bus.

In our case, we actually intervene by modifying the `publish_events()` method on `FakeUnitOfWork` and decoupling it from the real message bus, instead making it record what events it sees:

_Fake message bus implemented in UoW (tests/unit/test_handlers.py)_

```
class
```

Now when we invoke `messagebus.handle()` using the `FakeUnitOfWorkWithFakeMessageBus`, it runs only the handler for that event. So we can write a more isolated unit test: instead of checking all the side effects, we just check that `BatchQuantityChanged` leads to `AllocationRequired` if the quantity drops below the total already allocated:

_Testing reallocation in isolation (tests/unit/test_handlers.py)_

```
def
```

Whether you want to do this or not depends on the complexity of your chain of events. We say, start out with edge-to-edge testing, and resort to this only if necessary.

##### EXERCISE FOR THE READER

A great way to force yourself to really understand some code is to refactor it. In the discussion of testing handlers in isolation, we used something called `FakeUnitOfWorkWithFakeMessageBus`, which is unnecessarily complicated and violates the SRP.

If we change the message bus to being a class,[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#idm45846297135208) then building a `FakeMessageBus` is more straightforward:

_An abstract message bus and its real and fake versions_

```
class
```

So jump into the code on [GitHub](https://github.com/cosmicpython/code/tree/chapter_09_all_messagebus) and see if you can get a class-based version working, and then write a version of `test_reallocates_if_necessary_isolated()` from earlier.

We use a class-based message bus in [Chapter 13](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#chapter_13_dependency_injection), if you need more inspiration.

# Wrap-Up

Let’s look back at what we’ve achieved, and think about why we did it.

## What Have We Achieved?

Events are simple dataclasses that define the data structures for inputs and internal messages within our system. This is quite powerful from a DDD standpoint, since events often translate really well into business language (look up _event storming_ if you haven’t already).

Handlers are the way we react to events. They can call down to our model or call out to external services. We can define multiple handlers for a single event if we want to. Handlers can also raise other events. This allows us to be very granular about what a handler does and really stick to the SRP.

## Why Have We Achieved?

Our ongoing objective with these architectural patterns is to try to have the complexity of our application grow more slowly than its size. When we go all in on the message bus, as always we pay a price in terms of architectural complexity (see [Table 9-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#chapter_09_all_messagebus_tradeoffs)), but we buy ourselves a pattern that can handle almost arbitrarily complex requirements without needing any further conceptual or architectural change to the way we do things.

Here we’ve added quite a complicated use case (change quantity, deallocate, start new transaction, reallocate, publish external notification), but architecturally, there’s been no cost in terms of complexity. We’ve added new events, new handlers, and a new external adapter (for email), all of which are existing categories of _things_ in our architecture that we understand and know how to reason about, and that are easy to explain to newcomers. Our moving parts each have one job, they’re connected to each other in well-defined ways, and there are no unexpected side effects.

Table 9-1. Whole app is a message bus: the trade-offs
|Pros|Cons|
|---|---|
|- Handlers and services are the same thing, so that’s simpler.<br>    <br>- We have a nice data structure for inputs to the system.|- A message bus is still a slightly unpredictable way of doing things from a web point of view. You don’t know in advance when things are going to end.<br>    <br>- There will be duplication of fields and structure between model objects and events, which will have a maintenance cost. Adding a field to one usually means adding a field to at least one of the others.|

Now, you may be wondering, where are those `BatchQuantityChanged` events going to come from? The answer is revealed in a couple chapters’ time. But first, let’s talk about [events versus commands](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch10.html#chapter_10_commands).

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#idm45846299214296-marker) Event-based modeling is so popular that a practice called _event storming_ has been developed for facilitating event-based requirements gathering and domain model elaboration.

[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#idm45846299188664-marker) If you’ve done a bit of reading about event-driven architectures, you may be thinking, “Some of these events sound more like commands!” Bear with us! We’re trying to introduce one concept at a time. In the [next chapter](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch10.html#chapter_10_commands), we’ll introduce the distinction between commands and events.

[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#idm45846297135208-marker) The “simple” implementation in this chapter essentially uses the _messagebus.py_ module itself to implement the Singleton Pattern.
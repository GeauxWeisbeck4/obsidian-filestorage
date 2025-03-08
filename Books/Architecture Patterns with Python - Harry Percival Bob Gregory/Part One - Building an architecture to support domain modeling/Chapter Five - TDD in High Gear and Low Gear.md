# Chapter 5. TDD in High Gear and Low Gear

We’ve introduced the service layer to capture some of the additional orchestration responsibilities we need from a working application. The service layer helps us clearly define our use cases and the workflow for each: what we need to get from our repositories, what pre-checks and current state validation we should do, and what we save at the end.

But currently, many of our unit tests operate at a lower level, acting directly on the model. In this chapter we’ll discuss the trade-offs involved in moving those tests up to the service-layer level, and some more general testing guidelines.

##### HARRY SAYS: SEEING A TEST PYRAMID IN ACTION WAS A LIGHT-BULB MOMENT

Here are a few words from Harry directly:

_I was initially skeptical of all Bob’s architectural patterns, but seeing an actual test pyramid made me a convert._

_Once you implement domain modeling and the service layer, you really actually can get to a stage where unit tests outnumber integration and end-to-end tests by an order of magnitude. Having worked in places where the E2E test build would take hours (“wait ‘til tomorrow,” essentially), I can’t tell you what a difference it makes to be able to run all your tests in minutes or seconds._

_Read on for some guidelines on how to decide what kinds of tests to write and at which level. The high gear versus low gear way of thinking really changed my testing life._

# How Is Our Test Pyramid Looking?

Let’s see what this move to using a service layer, with its own service-layer tests, does to our test pyramid:

_Counting types of tests_

```
$ 
```

Not bad! We have 15 unit tests, 8 integration tests, and just 2 end-to-end tests. That’s already a healthy-looking test pyramid.

# Should Domain Layer Tests Move to the Service Layer?

Let’s see what happens if we take this a step further. Since we can test our software against the service layer, we don’t really need tests for the domain model anymore. Instead, we could rewrite all of the domain-level tests from [Chapter 1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch01.html#chapter_01_domain_model) in terms of the service layer:

_Rewriting a domain test at the service layer (tests/unit/test_services.py)_

```
# domain-layer test:
```

Why would we want to do that?

Tests are supposed to help us change our system fearlessly, but often we see teams writing too many tests against their domain model. This causes problems when they come to change their codebase and find that they need to update tens or even hundreds of unit tests.

This makes sense if you stop to think about the purpose of automated tests. We use tests to enforce that a property of the system doesn’t change while we’re working. We use tests to check that the API continues to return 200, that the database session continues to commit, and that orders are still being allocated.

If we accidentally change one of those behaviors, our tests will break. The flip side, though, is that if we want to change the design of our code, any tests relying directly on that code will also fail.

As we get further into the book, you’ll see how the service layer forms an API for our system that we can drive in multiple ways. Testing against this API reduces the amount of code that we need to change when we refactor our domain model. If we restrict ourselves to testing only against the service layer, we won’t have any tests that directly interact with “private” methods or attributes on our model objects, which leaves us freer to refactor them.

###### TIP

Every line of code that we put in a test is like a blob of glue, holding the system in a particular shape. The more low-level tests we have, the harder it will be to change things.

# On Deciding What Kind of Tests to Write

You might be asking yourself, “Should I rewrite all my unit tests, then? Is it wrong to write tests against the domain model?” To answer those questions, it’s important to understand the trade-off between coupling and design feedback (see [Figure 5-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#test_spectrum_diagram)).

![apwp 0501](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0501.png)

###### Figure 5-1. The test spectrum

Extreme programming (XP) exhorts us to “listen to the code.” When we’re writing tests, we might find that the code is hard to use or notice a code smell. This is a trigger for us to refactor, and to reconsider our design.

We only get that feedback, though, when we’re working closely with the target code. A test for the HTTP API tells us nothing about the fine-grained design of our objects, because it sits at a much higher level of abstraction.

On the other hand, we can rewrite our entire application and, so long as we don’t change the URLs or request formats, our HTTP tests will continue to pass. This gives us confidence that large-scale changes, like changing the database schema, haven’t broken our code.

At the other end of the spectrum, the tests we wrote in [Chapter 1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch01.html#chapter_01_domain_model) helped us to flesh out our understanding of the objects we need. The tests guided us to a design that makes sense and reads in the domain language. When our tests read in the domain language, we feel comfortable that our code matches our intuition about the problem we’re trying to solve.

Because the tests are written in the domain language, they act as living documentation for our model. A new team member can read these tests to quickly understand how the system works and how the core concepts interrelate.

We often “sketch” new behaviors by writing tests at this level to see how the code might look. When we want to improve the design of the code, though, we will need to replace or delete these tests, because they are tightly coupled to a particular implementation.

# High and Low Gear

Most of the time, when we are adding a new feature or fixing a bug, we don’t need to make extensive changes to the domain model. In these cases, we prefer to write tests against services because of the lower coupling and higher coverage.

For example, when writing an `add_stock` function or a `cancel_order` feature, we can work more quickly and with less coupling by writing tests against the service layer.

When starting a new project or when hitting a particularly gnarly problem, we will drop back down to writing tests against the domain model so we get better feedback and executable documentation of our intent.

The metaphor we use is that of shifting gears. When starting a journey, the bicycle needs to be in a low gear so that it can overcome inertia. Once we’re off and running, we can go faster and more efficiently by changing into a high gear; but if we suddenly encounter a steep hill or are forced to slow down by a hazard, we again drop down to a low gear until we can pick up speed again.

# Fully Decoupling the Service-Layer Tests from the Domain

We still have direct dependencies on the domain in our service-layer tests, because we use domain objects to set up our test data and to invoke our service-layer functions.

To have a service layer that’s fully decoupled from the domain, we need to rewrite its API to work in terms of primitives.

Our service layer currently takes an `OrderLine` domain object:

_Before: allocate takes a domain object (service_layer/services.py)_

```
def
```

How would it look if its parameters were all primitive types?

_After: allocate takes strings and ints (service_layer/services.py)_

```
def
```

We rewrite the tests in those terms as well:

_Tests now use primitives in function call (tests/unit/test_services.py)_

```
def
```

But our tests still depend on the domain, because we still manually instantiate `Batch` objects. So, if one day we decide to massively refactor how our `Batch` model works, we’ll have to change a bunch of tests.

## Mitigation: Keep All Domain Dependencies in Fixture Functions

We could at least abstract that out to a helper function or a fixture in our tests. Here’s one way you could do that, adding a factory function on `FakeRepository`:

_Factory functions for fixtures are one possibility (tests/unit/test_services.py)_

```
class
```

At least that would move all of our tests’ dependencies on the domain into one place.

## Adding a Missing Service

We could go one step further, though. If we had a service to add stock, we could use that and make our service-layer tests fully expressed in terms of the service layer’s official use cases, removing all dependencies on the domain:

_Test for new add_batch service (tests/unit/test_services.py)_

```
def
```

###### TIP

In general, if you find yourself needing to do domain-layer stuff directly in your service-layer tests, it may be an indication that your service layer is incomplete.

And the implementation is just two lines:

_A new service for add_batch (service_layer/services.py)_

```
def
```

###### NOTE

Should you write a new service just because it would help remove dependencies from your tests? Probably not. But in this case, we almost definitely would need an `add_batch` service one day anyway.

That now allows us to rewrite _all_ of our service-layer tests purely in terms of the services themselves, using only primitives, and without any dependencies on the model:

_Services tests now use only services (tests/unit/test_services.py)_

```
def
```

This is a really nice place to be in. Our service-layer tests depend on only the service layer itself, leaving us completely free to refactor the model as we see fit.

# Carrying the Improvement Through to the E2E Tests

In the same way that adding `add_batch` helped decouple our service-layer tests from the model, adding an API endpoint to add a batch would remove the need for the ugly `add_stock` fixture, and our E2E tests could be free of those hardcoded SQL queries and the direct dependency on the database.

Thanks to our service function, adding the endpoint is easy, with just a little JSON wrangling and a single function call required:

_API for adding a batch (entrypoints/flask_app.py)_

```
@app.route
```

###### NOTE

Are you thinking to yourself, POST to _/add_batch_? That’s not very RESTful! You’re quite right. We’re being happily sloppy, but if you’d like to make it all more RESTy, maybe a POST to _/batches_, then knock yourself out! Because Flask is a thin adapter, it’ll be easy. See [the next sidebar](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#types_of_test_rules_of_thumb).

And our hardcoded SQL queries from _conftest.py_ get replaced with some API calls, meaning the API tests have no dependencies other than the API, which is also nice:

_API tests can now add their own batches (tests/e2e/test_api.py)_

```
def
```

# Wrap-Up

Once you have a service layer in place, you really can move the majority of your test coverage to unit tests and develop a healthy test pyramid.

##### RECAP: RULES OF THUMB FOR DIFFERENT TYPES OF TEST

Aim for one end-to-end test per feature

This might be written against an HTTP API, for example. The objective is to demonstrate that the feature works, and that all the moving parts are glued together correctly.

Write the bulk of your tests against the service layer

These edge-to-edge tests offer a good trade-off between coverage, runtime, and efficiency. Each test tends to cover one code path of a feature and use fakes for I/O. This is the place to exhaustively cover all the edge cases and the ins and outs of your business logic.[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#idm45846305205304)

Maintain a small core of tests written against your domain model

These tests have highly focused coverage and are more brittle, but they have the highest feedback. Don’t be afraid to delete these tests if the functionality is later covered by tests at the service layer.

Error handling counts as a feature

Ideally, your application will be structured such that all errors that bubble up to your entrypoints (e.g., Flask) are handled in the same way. This means you need to test only the happy path for each feature, and to reserve one end-to-end test for all unhappy paths (and many unhappy path unit tests, of course).

A few things will help along the way:

- Express your service layer in terms of primitives rather than domain objects.
    
- In an ideal world, you’ll have all the services you need to be able to test entirely against the service layer, rather than hacking state via repositories or the database. This pays off in your end-to-end tests as well.
    

Onto the next chapter!

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#idm45846305205304-marker) A valid concern about writing tests at a higher level is that it can lead to combinatorial explosion for more complex use cases. In these cases, dropping down to lower-level unit tests of the various collaborating domain objects can be useful. But see also [Chapter 8](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch08.html#chapter_08_events_and_message_bus) and [“Optionally: Unit Testing Event Handlers in Isolation with a Fake Message Bus”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch09.html#fake_message_bus).
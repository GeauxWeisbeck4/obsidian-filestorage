# Chapter 4. Our First Use Case: Flask API and Service Layer

Back to our allocations project! [Figure 4-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#maps_service_layer_before) shows the point we reached at the end of [Chapter 2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch02.html#chapter_02_repository), which covered the Repository pattern.

![apwp 0401](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0401.png)

###### Figure 4-1. Before: we drive our app by talking to repositories and the domain model

In this chapter, we discuss the differences between orchestration logic, business logic, and interfacing code, and we introduce the _Service Layer_ pattern to take care of orchestrating our workflows and defining the use cases of our system.

We’ll also discuss testing: by combining the Service Layer with our repository abstraction over the database, we’re able to write fast tests, not just of our domain model but of the entire workflow for a use case.

[Figure 4-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#maps_service_layer_after) shows what we’re aiming for: we’re going to add a Flask API that will talk to the service layer, which will serve as the entrypoint to our domain model. Because our service layer depends on the `AbstractRepository`, we can unit test it by using `FakeRepository` but run our production code using `SqlAlchemyRepository`.

![apwp 0402](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0402.png)

###### Figure 4-2. The service layer will become the main way into our app

In our diagrams, we are using the convention that new components are highlighted with bold text/lines (and yellow/orange color, if you’re reading a digital version).

###### TIP

The code for this chapter is in the chapter_04_service_layer branch [on GitHub](https://oreil.ly/TBRuy):

git clone https://github.com/cosmicpython/code.git
cd code
git checkout chapter_04_service_layer
# or to code along, checkout Chapter 2:
git checkout chapter_02_repository

# Connecting Our Application to the Real World

Like any good agile team, we’re hustling to try to get an MVP out and in front of the users to start gathering feedback. We have the core of our domain model and the domain service we need to allocate orders, and we have the repository interface for permanent storage.

Let’s plug all the moving parts together as quickly as we can and then refactor toward a cleaner architecture. Here’s our plan:

1. Use Flask to put an API endpoint in front of our `allocate` domain service. Wire up the database session and our repository. Test it with an end-to-end test and some quick-and-dirty SQL to prepare test data.
    
2. Refactor out a service layer that can serve as an abstraction to capture the use case and that will sit between Flask and our domain model. Build some service-layer tests and show how they can use `FakeRepository`.
    
3. Experiment with different types of parameters for our service layer functions; show that using primitive data types allows the service layer’s clients (our tests and our Flask API) to be decoupled from the model layer.
    

# A First End-to-End Test

No one is interested in getting into a long terminology debate about what counts as an end-to-end (E2E) test versus a functional test versus an acceptance test versus an integration test versus a unit test. Different projects need different combinations of tests, and we’ve seen perfectly successful projects just split things into “fast tests” and “slow tests.”

For now, we want to write one or maybe two tests that are going to exercise a “real” API endpoint (using HTTP) and talk to a real database. Let’s call them _end-to-end tests_ because it’s one of the most self-explanatory names.

The following shows a first cut:

_A first API test (test_api.py)_

```
@pytest.mark.usefixtures
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO1-1)

`random_sku()`, `random_batchref()`, and so on are little helper functions that generate randomized characters by using the `uuid` module. Because we’re running against an actual database now, this is one way to prevent various tests and runs from interfering with each other.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO1-2)

`add_stock` is a helper fixture that just hides away the details of manually inserting rows into the database using SQL. We’ll show a nicer way of doing this later in the chapter.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO1-3)

_config.py_ is a module in which we keep configuration information.

Everyone solves these problems in different ways, but you’re going to need some way of spinning up Flask, possibly in a container, and of talking to a Postgres database. If you want to see how we did it, check out [Appendix B](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#appendix_project_structure).

# The Straightforward Implementation

Implementing things in the most obvious way, you might get something like this:

_First cut of Flask app (flask_app.py)_

```
from
```

So far, so good. No need for too much more of your “architecture astronaut” nonsense, Bob and Harry, you may be thinking.

But hang on a minute—there’s no commit. We’re not actually saving our allocation to the database. Now we need a second test, either one that will inspect the database state after (not very black-boxy), or maybe one that checks that we can’t allocate a second line if a first should have already depleted the batch:

_Test allocations are persisted (test_api.py)_

```
@pytest.mark.usefixtures
```

Not quite so lovely, but that will force us to add the commit.

# Error Conditions That Require Database Checks

If we keep going like this, though, things are going to get uglier and uglier.

Suppose we want to add a bit of error handling. What if the domain raises an error, for a SKU that’s out of stock? Or what about a SKU that doesn’t even exist? That’s not something the domain even knows about, nor should it. It’s more of a sanity check that we should implement at the database layer, before we even invoke the domain service.

Now we’re looking at two more end-to-end tests:

_Yet more tests at the E2E layer (test_api.py)_

```
@pytest.mark.usefixtures
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO2-1)

In the first test, we’re trying to allocate more units than we have in stock.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO2-2)

In the second, the SKU just doesn’t exist (because we never called `add_stock`), so it’s invalid as far as our app is concerned.

And sure, we could implement it in the Flask app too:

_Flask app starting to get crufty (flask_app.py)_

```
def
```

But our Flask app is starting to look a bit unwieldy. And our number of E2E tests is starting to get out of control, and soon we’ll end up with an inverted test pyramid (or “ice-cream cone model,” as Bob likes to call it).

# Introducing a Service Layer, and Using FakeRepository to Unit Test It

If we look at what our Flask app is doing, there’s quite a lot of what we might call _orchestration_—fetching stuff out of our repository, validating our input against database state, handling errors, and committing in the happy path. Most of these things don’t have anything to do with having a web API endpoint (you’d need them if you were building a CLI, for example; see [Appendix C](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app03.html#appendix_csvs)), and they’re not really things that need to be tested by end-to-end tests.

It often makes sense to split out a service layer, sometimes called an _orchestration layer_ or a _use-case layer_.

Do you remember the `FakeRepository` that we prepared in [Chapter 3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#chapter_03_abstractions)?

_Our fake repository, an in-memory collection of batches (test_services.py)_

```
class
```

Here’s where it will come in useful; it lets us test our service layer with nice, fast unit tests:

_Unit testing with fakes at the service layer (test_services.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO3-1)

`FakeRepository` holds the `Batch` objects that will be used by our test.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO3-2)

Our services module (_services.py_) will define an `allocate()` service-layer function. It will sit between our `allocate_endpoint()` function in the API layer and the `allocate()` domain service function from our domain model.[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#idm45846310783640)

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO3-3)

We also need a `FakeSession` to fake out the database session, as shown in the following code snippet.

_A fake database session (test_services.py)_

```
class
```

This fake session is only a temporary solution. We’ll get rid of it and make things even nicer soon, in [Chapter 6](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#chapter_06_uow). But in the meantime the fake `.commit()` lets us migrate a third test from the E2E layer:

_A second test at the service layer (test_services.py)_

```
def
```

## A Typical Service Function

We’ll write a service function that looks something like this:

_Basic allocation service (services.py)_

```
class
```

Typical service-layer functions have similar steps:

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO4-1)

We fetch some objects from the repository.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO4-2)

We make some checks or assertions about the request against the current state of the world.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO4-3)

We call a domain service.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO4-4)

If all is well, we save/update any state we’ve changed.

That last step is a little unsatisfactory at the moment, as our service layer is tightly coupled to our database layer. We’ll improve that in [Chapter 6](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#chapter_06_uow) with the Unit of Work pattern.

##### DEPEND ON ABSTRACTIONS

Notice one more thing about our service-layer function:

```
def
```

It depends on a repository. We’ve chosen to make the dependency explicit, and we’ve used the type hint to say that we depend on `AbstractRepository`. This means it’ll work both when the tests give it a `FakeRepository` and when the Flask app gives it a `SqlAlchemyRepository`.

If you remember [“The Dependency Inversion Principle”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/preface02.html#dip), this is what we mean when we say we should “depend on abstractions.” Our _high-level module_, the service layer, depends on the repository abstraction. And the _details_ of the implementation for our specific choice of persistent storage also depend on that same abstraction. See Figures [4-3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#service_layer_diagram_abstract_dependencies) and [4-4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#service_layer_diagram_test_dependencies).

See also in [Appendix C](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app03.html#appendix_csvs) a worked example of swapping out the _details_ of which persistent storage system to use while leaving the abstractions intact.

But the essentials of the service layer are there, and our Flask app now looks a lot cleaner:

_Flask app delegating to service layer (flask_app.py)_

```
@app.route
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO5-1)

We instantiate a database session and some repository objects.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO5-3)

We extract the user’s commands from the web request and pass them to a domain service.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO5-7)

We return some JSON responses with the appropriate status codes.

The responsibilities of the Flask app are just standard web stuff: per-request session management, parsing information out of POST parameters, response status codes, and JSON. All the orchestration logic is in the use case/service layer, and the domain logic stays in the domain.

Finally, we can confidently strip down our E2E tests to just two, one for the happy path and one for the unhappy path:

_E2E tests only happy and unhappy paths (test_api.py)_

```
@pytest.mark.usefixtures
```

We’ve successfully split our tests into two broad categories: tests about web stuff, which we implement end to end; and tests about orchestration stuff, which we can test against the service layer in memory.

##### EXERCISE FOR THE READER

Now that we have an allocate service, why not build out a service for `deallocate`? We’ve added [an E2E test and a few stub service-layer tests](https://github.com/cosmicpython/code/tree/chapter_04_service_layer_exercise) for you to get started on GitHub.

If that’s not enough, continue into the E2E tests and _flask_app.py_, and refactor the Flask adapter to be more RESTful. Notice how doing so doesn’t require any change to our service layer or domain layer!

###### TIP

If you decide you want to build a read-only endpoint for retrieving allocation info, just do “the simplest thing that can possibly work,” which is `repo.get()` right in the Flask handler. We’ll talk more about reads versus writes in [Chapter 12](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#chapter_12_cqrs).

# Why Is Everything Called a Service?

Some of you are probably scratching your heads at this point trying to figure out exactly what the difference is between a domain service and a service layer.

We’re sorry—we didn’t choose the names, or we’d have much cooler and friendlier ways to talk about this stuff.

We’re using two things called a _service_ in this chapter. The first is an _application service_ (our service layer). Its job is to handle requests from the outside world and to _orchestrate_ an operation. What we mean is that the service layer _drives_ the application by following a bunch of simple steps:

- Get some data from the database
    
- Update the domain model
    
- Persist any changes
    

This is the kind of boring work that has to happen for every operation in your system, and keeping it separate from business logic helps to keep things tidy.

The second type of service is a _domain service_. This is the name for a piece of logic that belongs in the domain model but doesn’t sit naturally inside a stateful entity or value object. For example, if you were building a shopping cart application, you might choose to build taxation rules as a domain service. Calculating tax is a separate job from updating the cart, and it’s an important part of the model, but it doesn’t seem right to have a persisted entity for the job. Instead a stateless TaxCalculator class or a `calculate_tax` function can do the job.

# Putting Things in Folders to See Where It All Belongs

As our application gets bigger, we’ll need to keep tidying our directory structure. The layout of our project gives us useful hints about what kinds of object we’ll find in each file.

Here’s one way we could organize things:

_Some subfolders_

```
.
├── config.py
├── domain  
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO6-1)

Let’s have a folder for our domain model. Currently that’s just one file, but for a more complex application, you might have one file per class; you might have helper parent classes for `Entity`, `ValueObject`, and `Aggregate`, and you might add an _exceptions.py_ for domain-layer exceptions and, as you’ll see in [Part II](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/part02.html#part2), _commands.py_ and _events.py_.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO6-2)

We’ll distinguish the service layer. Currently that’s just one file called _services.py_ for our service-layer functions. You could add service-layer exceptions here, and as you’ll see in [Chapter 5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#chapter_05_high_gear_low_gear), we’ll add _unit_of_work.py_.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO6-3)

_Adapters_ is a nod to the ports and adapters terminology. This will fill up with any other abstractions around external I/O (e.g., a _redis_client.py_). Strictly speaking, you would call these _secondary_ adapters or _driven_ adapters, or sometimes _inward-facing_ adapters.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#co_our_first_use_case___span_class__keep_together__flask_api_and_service_layer__span__CO6-4)

Entrypoints are the places we drive our application from. In the official ports and adapters terminology, these are adapters too, and are referred to as _primary_, _driving_, or _outward-facing_ adapters.

What about ports? As you may remember, they are the abstract interfaces that the adapters implement. We tend to keep them in the same file as the adapters that implement them.

# Wrap-Up

Adding the service layer has really bought us quite a lot:

- Our Flask API endpoints become very thin and easy to write: their only responsibility is doing “web stuff,” such as parsing JSON and producing the right HTTP codes for happy or unhappy cases.
    
- We’ve defined a clear API for our domain, a set of use cases or entrypoints that can be used by any adapter without needing to know anything about our domain model classes—whether that’s an API, a CLI (see [Appendix C](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app03.html#appendix_csvs)), or the tests! They’re an adapter for our domain too.
    
- We can write tests in “high gear” by using the service layer, leaving us free to refactor the domain model in any way we see fit. As long as we can still deliver the same use cases, we can experiment with new designs without needing to rewrite a load of tests.
    
- And our test pyramid is looking good—the bulk of our tests are fast unit tests, with just the bare minimum of E2E and integration tests.
    

## The DIP in Action

[Figure 4-3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#service_layer_diagram_abstract_dependencies) shows the dependencies of our service layer: the domain model and `AbstractRepository` (the port, in ports and adapters terminology).

When we run the tests, [Figure 4-4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#service_layer_diagram_test_dependencies) shows how we implement the abstract dependencies by using `FakeRepository` (the adapter).

And when we actually run our app, we swap in the “real” dependency shown in [Figure 4-5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#service_layer_diagram_runtime_dependencies).

![apwp 0403](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0403.png)

###### Figure 4-3. Abstract dependencies of the service layer

![apwp 0404](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0404.png)

###### Figure 4-4. Tests provide an implementation of the abstract dependency

![apwp 0405](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0405.png)

###### Figure 4-5. Dependencies at runtime

Wonderful.

Let’s pause for [Table 4-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#chapter_04_service_layer_tradeoffs), in which we consider the pros and cons of having a service layer at all.

Table 4-1. Service layer: the trade-offs
|Pros|Cons|
|---|---|
|- We have a single place to capture all the use cases for our application.<br>    <br>- We’ve placed our clever domain logic behind an API, which leaves us free to refactor.<br>    <br>- We have cleanly separated “stuff that talks HTTP” from “stuff that talks allocation.”<br>    <br>- When combined with the Repository pattern and `FakeRepository`, we have a nice way of writing tests at a higher level than the domain layer; we can test more of our workflow without needing to use integration tests (read on to [Chapter 5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#chapter_05_high_gear_low_gear) for more elaboration on this).|- If your app is _purely_ a web app, your controllers/view functions can be the single place to capture all the use cases.<br>    <br>- It’s yet another layer of abstraction.<br>    <br>- Putting too much logic into the service layer can lead to the _Anemic Domain_ anti-pattern. It’s better to introduce this layer after you spot orchestration logic creeping into your controllers.<br>    <br>- You can get a lot of the benefits that come from having rich domain models by simply pushing logic out of your controllers and down to the model layer, without needing to add an extra layer in between (aka “fat models, thin controllers”).|

But there are still some bits of awkwardness to tidy up:

- The service layer is still tightly coupled to the domain, because its API is expressed in terms of `OrderLine` objects. In [Chapter 5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#chapter_05_high_gear_low_gear), we’ll fix that and talk about the way that the service layer enables more productive TDD.
    
- The service layer is tightly coupled to a `session` object. In [Chapter 6](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#chapter_06_uow), we’ll introduce one more pattern that works closely with the Repository and Service Layer patterns, the Unit of Work pattern, and everything will be absolutely lovely. You’ll see!
    

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#idm45846310783640-marker) Service-layer services and domain services do have confusingly similar names. We tackle this topic later in [“Why Is Everything Called a Service?”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#why_is_everything_a_service).
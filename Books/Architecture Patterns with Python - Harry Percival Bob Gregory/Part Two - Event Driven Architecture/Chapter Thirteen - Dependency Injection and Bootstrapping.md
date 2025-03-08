# Chapter 13. Dependency Injection (and Bootstrapping)

Dependency injection (DI) is regarded with suspicion in the Python world. And we’ve managed _just fine_ without it so far in the example code for this book!

In this chapter, we’ll explore some of the pain points in our code that lead us to consider using DI, and we’ll present some options for how to do it, leaving it to you to pick which you think is most Pythonic.

We’ll also add a new component to our architecture called _bootstrap.py_; it will be in charge of dependency injection, as well as some other initialization stuff that we often need. We’ll explain why this sort of thing is called a _composition root_ in OO languages, and why _bootstrap script_ is just fine for our purposes.

[Figure 13-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#bootstrap_chapter_before_diagram) shows what our app looks like without a bootstrapper: the entrypoints do a lot of initialization and passing around of our main dependency, the UoW.

###### TIP

If you haven’t already, it’s worth reading [Chapter 3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#chapter_03_abstractions) before continuing with this chapter, particularly the discussion of functional versus object-oriented dependency management.

![apwp 1301](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_1301.png)

###### Figure 13-1. Without bootstrap: entrypoints do a lot

###### TIP

The code for this chapter is in the chapter_13_dependency_injection branch [on GitHub](https://oreil.ly/-B7e6):

git clone https://github.com/cosmicpython/code.git
cd code
git checkout chapter_13_dependency_injection
# or to code along, checkout the previous chapter:
git checkout chapter_12_cqrs

[Figure 13-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#bootstrap_chapter_after_diagram) shows our bootstrapper taking over those responsibilities.

![apwp 1302](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_1302.png)

###### Figure 13-2. Bootstrap takes care of all that in one place

# Implicit Versus Explicit Dependencies

Depending on your particular brain type, you may have a slight feeling of unease at the back of your mind at this point. Let’s bring it out into the open. We’ve shown you two ways of managing dependencies and testing them.

For our database dependency, we’ve built a careful framework of explicit dependencies and easy options for overriding them in tests. Our main handler functions declare an explicit dependency on the UoW:

_Our handlers have an explicit dependency on the UoW (src/allocation/service_layer/handlers.py)_

```
def
```

And that makes it easy to swap in a fake UoW in our service-layer tests:

_Service-layer tests against a fake UoW: (tests/unit/test_services.py)_

    `uow` `=` `FakeUnitOfWork``()`
    `messagebus``.``handle``([``...``],` `uow``)`

The UoW itself declares an explicit dependency on the session factory:

_The UoW depends on a session factory (src/allocation/service_layer/unit_of_work.py)_

```
class
```

We take advantage of it in our integration tests to be able to sometimes use SQLite instead of Postgres:

_Integration tests against a different DB (tests/integration/test_uow.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO1-1)

Integration tests swap out the default Postgres `session_factory` for a SQLite one.

# Aren’t Explicit Dependencies Totally Weird and Java-y?

If you’re used to the way things normally happen in Python, you’ll be thinking all this is a bit weird. The standard way to do things is to declare our dependency implicitly by simply importing it, and then if we ever need to change it for tests, we can monkeypatch, as is Right and True in dynamic languages:

_Email sending as a normal import-based dependency (src/allocation/service_layer/handlers.py)_

```
from
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO2-1)

Hardcoded import

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO2-2)

Calls specific email sender directly

Why pollute our application code with unnecessary arguments just for the sake of our tests? `mock.patch` makes monkeypatching nice and easy:

_mock dot patch, thank you Michael Foord (tests/unit/test_handlers.py)_

    `with` `mock``.``patch``(``"allocation.adapters.email.send"``)` `as` `mock_send_mail``:`
        `...`

The trouble is that we’ve made it look easy because our toy example doesn’t send real email (`email.send_mail` just does a `print`), but in real life, you’d end up having to call `mock.patch` for _every single test_ that might cause an out-of-stock notification. If you’ve worked on codebases with lots of mocks used to prevent unwanted side effects, you’ll know how annoying that mocky boilerplate gets.

And you’ll know that mocks tightly couple us to the implementation. By choosing to monkeypatch `email.send_mail`, we are tied to doing `import email`, and if we ever want to do `from email import send_mail`, a trivial refactor, we’d have to change all our mocks.

So it’s a trade-off. Yes, declaring explicit dependencies is unnecessary, strictly speaking, and using them would make our application code marginally more complex. But in return, we’d get tests that are easier to write and manage.

On top of that, declaring an explicit dependency is an example of the dependency inversion principle—rather than having an (implicit) dependency on a _specific_ detail, we have an (explicit) dependency on an _abstraction_:

> Explicit is better than implicit.
> 
> The Zen of Python

_The explicit dependency is more abstract (src/allocation/service_layer/handlers.py)_

```
def
```

But if we do change to declaring all these dependencies explicitly, who will inject them, and how? So far, we’ve really been dealing with only passing the UoW around: our tests use `FakeUnitOfWork`, while Flask and Redis eventconsumer entrypoints use the real UoW, and the message bus passes them onto our command handlers. If we add real and fake email classes, who will create them and pass them on?

That’s extra (duplicated) cruft for Flask, Redis, and our tests. Moreover, putting all the responsibility for passing dependencies to the right handler onto the message bus feels like a violation of the SRP.

Instead, we’ll reach for a pattern called _Composition Root_ (a bootstrap script to you and me),[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#idm45846292071448) and we’ll do a bit of “manual DI” (dependency injection without a framework). See [Figure 13-3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#bootstrap_new_image).[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#idm45846292069160)

![apwp 1303](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_1303.png)

###### Figure 13-3. Bootstrapper between entrypoints and message bus

# Preparing Handlers: Manual DI with Closures and Partials

One way to turn a function with dependencies into one that’s ready to be called later with those dependencies _already injected_ is to use closures or partial functions to compose the function with its dependencies:

_Examples of DI using closures or partial functions_

```
# existing allocate function, with abstract uow dependency
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO3-1)

The difference between closures (lambdas or named functions) and `functools.partial` is that the former use [late binding of variables](https://docs.python-guide.org/writing/gotchas/#late-binding-closures), which can be a source of confusion if any of the dependencies are mutable.

Here’s the same pattern again for the `send_out_of_stock_notification()` handler, which has different dependencies:

_Another closure and partial functions example_

```
def
```

# An Alternative Using Classes

Closures and partial functions will feel familiar to people who’ve done a bit of functional programming. Here’s an alternative using classes, which may appeal to others. It requires rewriting all our handler functions as classes, though:

_DI using classes_

```
# we replace the old `def allocate(cmd, uow)` with:
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO4-2)

The class is designed to produce a callable function, so it has a `_call_` method.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO4-1)

But we use the `init` to declare the dependencies it requires. This sort of thing will feel familiar if you’ve ever made class-based descriptors, or a class-based context manager that takes arguments.

Use whichever you and your team feel more comfortable with.

# A Bootstrap Script

We want our bootstrap script to do the following:

1. Declare default dependencies but allow us to override them
    
2. Do the “init” stuff that we need to get our app started
    
3. Inject all the dependencies into our handlers
    
4. Give us back the core object for our app, the message bus
    

Here’s a first cut:

_A bootstrap function (src/allocation/bootstrap.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO5-1)

`orm.start_mappers()` is our example of initialization work that needs to be done once at the beginning of an app. We also see things like setting up the `logging` module.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO5-2)

We can use the argument defaults to define what the normal/production defaults are. It’s nice to have them in a single place, but sometimes dependencies have some side effects at construction time, in which case you might prefer to default them to `None` instead.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO5-4)

We build up our injected versions of the handler mappings by using a function called `inject_dependencies()`, which we’ll show next.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO5-6)

We return a configured message bus ready for use.

Here’s how we inject dependencies into a handler function by inspecting it:

_DI by inspecting function signatures (src/allocation/bootstrap.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO6-1)

We inspect our command/event handler’s arguments.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO6-2)

We match them by name to our dependencies.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO6-3)

We inject them as kwargs to produce a partial.

##### EVEN-MORE-MANUAL DI WITH LESS MAGIC

If you’re finding the preceding `inspect` code a little harder to grok, this even simpler version may appeal to you.

Harry wrote the code for `inject_dependencies()` as a first cut of how to do “manual” dependency injection, and when he saw it, Bob accused him of overengineering and writing his own DI framework.

It honestly didn’t even occur to Harry that you could do it any more plainly, but you can, like this:

_Manually creating partial functions inline (src/allocation/bootstrap.py)_

    `injected_event_handlers` `=` `{`
        `events``.``Allocated``:` `[`
            `lambda` `e``:` `handlers``.``publish_allocated_event``(``e``,` `publish``),`
            `lambda` `e``:` `handlers``.``add_allocation_to_read_model``(``e``,` `uow``),`
        `],`
        `events``.``Deallocated``:` `[`
            `lambda` `e``:` `handlers``.``remove_allocation_from_read_model``(``e``,` `uow``),`
            `lambda` `e``:` `handlers``.``reallocate``(``e``,` `uow``),`
        `],`
        `events``.``OutOfStock``:` `[`
            `lambda` `e``:` `handlers``.``send_out_of_stock_notification``(``e``,` `send_mail``)`
        `]`
    `}`
    `injected_command_handlers` `=` `{`
        `commands``.``Allocate``:` `lambda` `c``:` `handlers``.``allocate``(``c``,` `uow``),`
        `commands``.``CreateBatch``:` \
            `lambda` `c``:` `handlers``.``add_batch``(``c``,` `uow``),`
        `commands``.``ChangeBatchQuantity``:` \
            `lambda` `c``:` `handlers``.``change_batch_quantity``(``c``,` `uow``),`
    `}`

Harry says he couldn’t even imagine writing out that many lines of code and having to look up that many function arguments manually. This is a perfectly viable solution, though, since it’s only one line of code or so per handler you add, and thus not a massive maintenance burden even if you have dozens of handlers.

Our app is structured in such a way that we always want to do dependency injection in only one place, the handler functions, so this super-manual solution and Harry’s `inspect()`-based one will both work fine.

If you find yourself wanting to do DI in more things and at different times, or if you ever get into _dependency chains_ (in which your dependencies have their own dependencies, and so on), you may get some mileage out of a “real” DI framework.

At MADE, we’ve used [Inject](https://pypi.org/project/Inject) in a few places, and it’s fine, although it makes Pylint unhappy. You might also check out [Punq](https://pypi.org/project/punq), as written by Bob himself, or the DRY-Python crew’s [dependencies](https://github.com/dry-python/dependencies).

# Message Bus Is Given Handlers at Runtime

Our message bus will no longer be static; it needs to have the already-injected handlers given to it. So we turn it from being a module into a configurable class:

_MessageBus as a class (src/allocation/service_layer/messagebus.py)_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO7-1)

The message bus becomes a class…

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO7-2)

…which is given its already-dependency-injected handlers.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO7-4)

The main `handle()` function is substantially the same, with just a few attributes and methods moved onto `self`.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO7-5)

Using `self.queue` like this is not thread-safe, which might be a problem if you’re using threads, because the bus instance is global in the Flask app context as we’ve written it. Just something to watch out for.

What else changes in the bus?

_Event and command handler logic stays the same (src/allocation/service_layer/messagebus.py)_

```
    
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO8-1)

`handle_event` and `handle_command` are substantially the same, but instead of indexing into a static `EVENT_HANDLERS` or `COMMAND_HANDLERS` dict, they use the versions on `self`.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO8-2)

Instead of passing a UoW into the handler, we expect the handlers to already have all their dependencies, so all they need is a single argument, the specific event or command.

# Using Bootstrap in Our Entrypoints

In our application’s entrypoints, we now just call `bootstrap.bootstrap()` and get a message bus that’s ready to go, rather than configuring a UoW and the rest of it:

_Flask calls bootstrap (src/allocation/entrypoints/flask_app.py)_

```
-from allocation import views
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO9-1)

We no longer need to call `start_orm()`; the bootstrap script’s initialization stages will do that.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO9-2)

We no longer need to explicitly build a particular type of UoW; the bootstrap script defaults take care of it.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO9-3)

And our message bus is now a specific instance rather than the global module.[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#idm45846290676024)

# Initializing DI in Our Tests

In tests, we can use `bootstrap.bootstrap()` with overridden defaults to get a custom message bus. Here’s an example in an integration test:

_Overriding bootstrap defaults (tests/integration/test_views.py)_

```
@pytest.fixture
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO10-1)

We do still want to start the ORM…

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO10-2)

…because we’re going to use a real UoW, albeit with an in-memory database.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO10-3)

But we don’t need to send email or publish, so we make those noops.

In our unit tests, in contrast, we can reuse our `FakeUnitOfWork`:

_Bootstrap in unit test (tests/unit/test_handlers.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO11-1)

No need to start the ORM…

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO11-2)

…because the fake UoW doesn’t use one.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO11-3)

We want to fake out our email and Redis adapters too.

So that gets rid of a little duplication, and we’ve moved a bunch of setup and sensible defaults into a single place.

##### EXERCISE FOR THE READER 1

Change all the handlers to being classes as per the [DI using classes](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#di_with_classes) example, and amend the bootstrapper’s DI code as appropriate. This will let you know whether you prefer the functional approach or the class-based approach when it comes to your own projects.

# Building an Adapter “Properly”: A Worked Example

To really get a feel for how it all works, let’s work through an example of how you might “properly” build an adapter and do dependency injection for it.

At the moment, we have two types of dependencies:

_Two types of dependencies (src/allocation/service_layer/messagebus.py)_

```
    
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO12-1)

The UoW has an abstract base class. This is the heavyweight option for declaring and managing your external dependency. We’d use this for the case when the dependency is relatively complex.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO12-2)

Our email sender and pub/sub publisher are defined as functions. This works just fine for simple dependencies.

Here are some of the things we find ourselves injecting at work:

- An S3 filesystem client
    
- A key/value store client
    
- A `requests` session object
    

Most of these will have more-complex APIs that you can’t capture as a single function: read and write, GET and POST, and so on.

Even though it’s simple, let’s use `send_mail` as an example to talk through how you might define a more complex dependency.

## Define the Abstract and Concrete Implementations

We’ll imagine a more generic notifications API. Could be email, could be SMS, could be Slack posts one day.

_An ABC and a concrete implementation (src/allocation/adapters/notifications.py)_

```
class
```

We change the dependency in the bootstrap script:

_Notifications in message bus (src/allocation/bootstrap.py)_

 def bootstrap(
     start_orm: bool = True,
     uow: unit_of_work.AbstractUnitOfWork = unit_of_work.SqlAlchemyUnitOfWork(),
`-    send_mail: Callable = email.send,`
`+    notifications: AbstractNotifications = EmailNotifications(),`
     publish: Callable = redis_eventpublisher.publish,
 ) -> messagebus.MessageBus:

## Make a Fake Version for Your Tests

We work through and define a fake version for unit testing:

_Fake notifications (tests/unit/test_handlers.py)_

```
class
```

And we use it in our tests:

_Tests change slightly (tests/unit/test_handlers.py)_

    `def` `test_sends_email_on_out_of_stock_error``(``self``):`
        `fake_notifs` `=` `FakeNotifications``()`
        `bus` `=` `bootstrap``.``bootstrap``(`
            `start_orm``=``False``,`
            `uow``=``FakeUnitOfWork``(),`
            `notifications``=``fake_notifs``,`
            `publish``=``lambda` `*``args``:` `None``,`
        `)`
        `bus``.``handle``(``commands``.``CreateBatch``(``"b1"``,` `"POPULAR-CURTAINS"``,` `9``,` `None``))`
        `bus``.``handle``(``commands``.``Allocate``(``"o1"``,` `"POPULAR-CURTAINS"``,` `10``))`
        `assert` `fake_notifs``.``sent``[``'stock@made.com'``]` `==` `[`
            `f``"Out of stock for POPULAR-CURTAINS"``,`
        `]`

## Figure Out How to Integration Test the Real Thing

Now we test the real thing, usually with an end-to-end or integration test. We’ve used [MailHog](https://github.com/mailhog/MailHog) as a real-ish email server for our Docker dev environment:

_Docker-compose config with real fake email server (docker-compose.yml)_

```
version
```

In our integration tests, we use the real `EmailNotifications` class, talking to the MailHog server in the Docker cluster:

_Integration test for email (tests/integration/test_email.py)_

```
@pytest.fixture
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO13-1)

We use our bootstrapper to build a message bus that talks to the real notifications class.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO13-2)

We figure out how to fetch emails from our “real” email server.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO13-3)

We use the bus to do our test setup.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#co_dependency_injection__and_bootstrapping__CO13-4)

Against all the odds, this actually worked, pretty much at the first go!

And that’s it really.

##### EXERCISE FOR THE READER 2

You could do two things for practice regarding adapters:

1. Try swapping out our notifications from email to SMS notifications using Twilio, for example, or Slack notifications. Can you find a good equivalent to MailHog for integration testing?
    
2. In a similar way to what we did moving from `send_mail` to a `Notifications` class, try refactoring our `redis_eventpublisher` that is currently just a `Callable` to some sort of more formal adapter/base class/protocol.
    

# Wrap-Up

Once you have more than one adapter, you’ll start to feel a lot of pain from passing dependencies around manually, unless you do some kind of _dependency injection._

Setting up dependency injection is just one of many typical setup/initialization activities that you need to do just once when starting your app. Putting this all together into a _bootstrap script_ is often a good idea.

The bootstrap script is also good as a place to provide sensible default configuration for your adapters, and as a single place to override those adapters with fakes for your tests.

A dependency injection framework can be useful if you find yourself needing to do DI at multiple levels—if you have chained dependencies of components that all need DI, for example.

This chapter also presented a worked example of changing an implicit/simple dependency into a “proper” adapter, factoring out an ABC, defining its real and fake implementations, and thinking through integration testing.

##### DI AND BOOTSTRAP RECAP

In summary:

1. Define your API using an ABC.
    
2. Implement the real thing.
    
3. Build a fake and use it for unit/service-layer/handler tests.
    
4. Find a less fake version you can put into your Docker environment.
    
5. Test the less fake “real” thing.
    
6. Profit!
    

These were the last patterns we wanted to cover, which brings us to the end of [Part II](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/part02.html#part2). In [the epilogue](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#epilogue_1_how_to_get_there_from_here), we’ll try to give you some pointers for applying these techniques in the Real WorldTM.

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#idm45846292071448-marker) Because Python is not a “pure” OO language, Python developers aren’t necessarily used to the concept of needing to _compose_ a set of objects into a working application. We just pick our entrypoint and run code from top to bottom.

[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#idm45846292069160-marker) Mark Seemann calls this [_Pure DI_](https://oreil.ly/iGpDL) or sometimes _Vanilla DI_.

[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#idm45846290676024-marker) However, it’s still a global in the `flask_app` module scope, if that makes sense. This may cause problems if you ever find yourself wanting to test your Flask app in-process by using the Flask Test Client instead of using Docker as we do. It’s worth researching [Flask app factories](https://oreil.ly/_a6Kl) if you get into this.
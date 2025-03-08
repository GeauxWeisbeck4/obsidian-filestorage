# Chapter 6. Unit of Work Pattern

In this chapter we’ll introduce the final piece of the puzzle that ties together the Repository and Service Layer patterns: the _Unit of Work_ pattern.

If the Repository pattern is our abstraction over the idea of persistent storage, the Unit of Work (UoW) pattern is our abstraction over the idea of _atomic operations_. It will allow us to finally and fully decouple our service layer from the data layer.

[Figure 6-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#before_uow_diagram) shows that, currently, a lot of communication occurs across the layers of our infrastructure: the API talks directly to the database layer to start a session, it talks to the repository layer to initialize `SQLAlchemyRepository`, and it talks to the service layer to ask it to allocate.

###### TIP

The code for this chapter is in the chapter_06_uow branch [on GitHub](https://oreil.ly/MoWdZ):

git clone https://github.com/cosmicpython/code.git
cd code
git checkout chapter_06_uow
# or to code along, checkout Chapter 4:
git checkout chapter_04_service_layer

![apwp 0601](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0601.png)

###### Figure 6-1. Without UoW: API talks directly to three layers

[Figure 6-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#after_uow_diagram) shows our target state. The Flask API now does only two things: it initializes a unit of work, and it invokes a service. The service collaborates with the UoW (we like to think of the UoW as being part of the service layer), but neither the service function itself nor Flask now needs to talk directly to the database.

And we’ll do it all using a lovely piece of Python syntax, a context manager.

![apwp 0602](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0602.png)

###### Figure 6-2. With UoW: UoW now manages database state

# The Unit of Work Collaborates with the Repository

Let’s see the unit of work (or UoW, which we pronounce “you-wow”) in action. Here’s how the service layer will look when we’re finished:

_Preview of unit of work in action (src/allocation/service_layer/services.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO1-1)

We’ll start a UoW as a context manager.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO1-2)

`uow.batches` is the batches repo, so the UoW provides us access to our permanent storage.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO1-3)

When we’re done, we commit or roll back our work, using the UoW.

The UoW acts as a single entrypoint to our persistent storage, and it keeps track of what objects were loaded and of the latest state.[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#idm45846304958120)

This gives us three useful things:

- A stable snapshot of the database to work with, so the objects we use aren’t changing halfway through an operation
    
- A way to persist all of our changes at once, so if something goes wrong, we don’t end up in an inconsistent state
    
- A simple API to our persistence concerns and a handy place to get a repository
    

# Test-Driving a UoW with Integration Tests

Here are our integration tests for the UOW:

_A basic “round-trip” test for a UoW (tests/integration/test_uow.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO2-1)

We initialize the UoW by using our custom session factory and get back a `uow` object to use in our `with` block.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO2-2)

The UoW gives us access to the batches repository via `uow.batches`.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO2-3)

We call `commit()` on it when we’re done.

For the curious, the `insert_batch` and `get_allocated_batch_ref` helpers look like this:

_Helpers for doing SQL stuff (tests/integration/test_uow.py)_

```
def
```

# Unit of Work and Its Context Manager

In our tests we’ve implicitly defined an interface for what a UoW needs to do. Let’s make that explicit by using an abstract base class:

_Abstract UoW context manager (src/allocation/service_layer/unit_of_work.py)_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO3-1)

The UoW provides an attribute called `.batches`, which will give us access to the batches repository.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO3-2)

If you’ve never seen a context manager, `__enter__` and `__exit__` are the two magic methods that execute when we enter the `with` block and when we exit it, respectively. They’re our setup and teardown phases.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO3-4)

We’ll call this method to explicitly commit our work when we’re ready.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO3-3)

If we don’t commit, or if we exit the context manager by raising an error, we do a `rollback`. (The rollback has no effect if `commit()` has been called. Read on for more discussion of this.)

## The Real Unit of Work Uses SQLAlchemy Sessions

The main thing that our concrete implementation adds is the database session:

_The real SQLAlchemy UoW (src/allocation/service_layer/unit_of_work.py)_

```
DEFAULT_SESSION_FACTORY
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO4-1)

The module defines a default session factory that will connect to Postgres, but we allow that to be overridden in our integration tests so that we can use SQLite instead.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO4-3)

The `__enter__` method is responsible for starting a database session and instantiating a real repository that can use that session.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO4-5)

We close the session on exit.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO4-6)

Finally, we provide concrete `commit()` and `rollback()` methods that use our database session.

## Fake Unit of Work for Testing

Here’s how we use a fake UoW in our service-layer tests:

_Fake UoW (tests/unit/test_services.py)_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO5-1)

`FakeUnitOfWork` and `FakeRepository` are tightly coupled, just like the real `UnitofWork` and `Repository` classes. That’s fine because we recognize that the objects are collaborators.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO5-2)

Notice the similarity with the fake `commit()` function from `FakeSession` (which we can now get rid of). But it’s a substantial improvement because we’re now faking out code that we wrote rather than third-party code. Some people say, [“Don’t mock what you don’t own”](https://oreil.ly/0LVj3).

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO5-4)

In our tests, we can instantiate a UoW and pass it to our service layer, rather than passing a repository and a session. This is considerably less cumbersome.

##### DON’T MOCK WHAT YOU DON’T OWN

Why do we feel more comfortable mocking the UoW than the session? Both of our fakes achieve the same thing: they give us a way to swap out our persistence layer so we can run tests in memory instead of needing to talk to a real database. The difference is in the resulting design.

If we cared only about writing tests that run quickly, we could create mocks that replace SQLAlchemy and use those throughout our codebase. The problem is that `Session` is a complex object that exposes lots of persistence-related functionality. It’s easy to use `Session` to make arbitrary queries against the database, but that quickly leads to data access code being sprinkled all over the codebase. To avoid that, we want to limit access to our persistence layer so each component has exactly what it needs and nothing more.

By coupling to the `Session` interface, you’re choosing to couple to all the complexity of SQLAlchemy. Instead, we want to choose a simpler abstraction and use that to clearly separate responsibilities. Our UoW is much simpler than a session, and we feel comfortable with the service layer being able to start and stop units of work.

“Don’t mock what you don’t own” is a rule of thumb that forces us to build these simple abstractions over messy subsystems. This has the same performance benefit as mocking the SQLAlchemy session but encourages us to think carefully about our designs.

# Using the UoW in the Service Layer

Here’s what our new service layer looks like:

_Service layer using UoW (src/allocation/service_layer/services.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO6-1)

Our service layer now has only the one dependency, once again on an _abstract_ UoW.

# Explicit Tests for Commit/Rollback Behavior

To convince ourselves that the commit/rollback behavior works, we wrote a couple of tests:

_Integration tests for rollback behavior (tests/integration/test_uow.py)_

```
def
```

###### TIP

We haven’t shown it here, but it can be worth testing some of the more “obscure” database behavior, like transactions, against the “real” database—that is, the same engine. For now, we’re getting away with using SQLite instead of Postgres, but in [Chapter 7](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch07.html#chapter_07_aggregate), we’ll switch some of the tests to using the real database. It’s convenient that our UoW class makes that easy!

# Explicit Versus Implicit Commits

Now we briefly digress on different ways of implementing the UoW pattern.

We could imagine a slightly different version of the UoW that commits by default and rolls back only if it spots an exception:

_A UoW with implicit commit… (src/allocation/unit_of_work.py)_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO7-1)

Should we have an implicit commit in the happy path?

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO7-2)

And roll back only on exception?

It would allow us to save a line of code and to remove the explicit commit from our client code:

_...would save us a line of code (src/allocation/service_layer/services.py)_

```
def
```

This is a judgment call, but we tend to prefer requiring the explicit commit so that we have to choose when to flush state.

Although we use an extra line of code, this makes the software safe by default. The default behavior is to _not change anything_. In turn, that makes our code easier to reason about because there’s only one code path that leads to changes in the system: total success and an explicit commit. Any other code path, any exception, any early exit from the UoW’s scope leads to a safe state.

Similarly, we prefer to roll back by default because it’s easier to understand; this rolls back to the last commit, so either the user did one, or we blow their changes away. Harsh but simple.

# Examples: Using UoW to Group Multiple Operations into an Atomic Unit

Here are a few examples showing the Unit of Work pattern in use. You can see how it leads to simple reasoning about what blocks of code happen together.

## Example 1: Reallocate

Suppose we want to be able to deallocate and then reallocate orders:

_Reallocate service function_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO8-1)

If `deallocate()` fails, we don’t want to call `allocate()`, obviously.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO8-2)

If `allocate()` fails, we probably don’t want to actually commit the `deallocate()` either.

## Example 2: Change Batch Quantity

Our shipping company gives us a call to say that one of the container doors opened, and half our sofas have fallen into the Indian Ocean. Oops!

_Change quantity_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#co_unit_of_work_pattern_CO9-1)

Here we may need to deallocate any number of lines. If we get a failure at any stage, we probably want to commit none of the changes.

# Tidying Up the Integration Tests

We now have three sets of tests, all essentially pointing at the database: _test_orm.py_, _test_repository.py_, and _test_uow.py_. Should we throw any away?

└── tests
    ├── conftest.py
    ├── e2e
    │   └── test_api.py
    ├── integration
    │   ├── test_orm.py
    │   ├── test_repository.py
    │   └── test_uow.py
    ├── pytest.ini
    └── unit
        ├── test_allocate.py
        ├── test_batches.py
        └── test_services.py

You should always feel free to throw away tests if you think they’re not going to add value longer term. We’d say that _test_orm.py_ was primarily a tool to help us learn SQLAlchemy, so we won’t need that long term, especially if the main things it’s doing are covered in _test_repository.py_. That last test, you might keep around, but we could certainly see an argument for just keeping everything at the highest possible level of abstraction (just as we did for the unit tests).

##### EXERCISE FOR THE READER

For this chapter, probably the best thing to try is to implement a UoW from scratch. The code, as always, is [on GitHub](https://github.com/cosmicpython/code/tree/chapter_06_uow_exercise). You could either follow the model we have quite closely, or perhaps experiment with separating the UoW (whose responsibilities are `commit()`, `rollback()`, and providing the `.batches` repository) from the context manager, whose job is to initialize things, and then do the commit or rollback on exit. If you feel like going all-functional rather than messing about with all these classes, you could use `@contextmanager` from `contextlib`.

We’ve stripped out both the actual UoW and the fakes, as well as paring back the abstract UoW. Why not send us a link to your repo if you come up with something you’re particularly proud of?

###### TIP

This is another example of the lesson from [Chapter 5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#chapter_05_high_gear_low_gear): as we build better abstractions, we can move our tests to run against them, which leaves us free to change the underlying details.

# Wrap-Up

Hopefully we’ve convinced you that the Unit of Work pattern is useful, and that the context manager is a really nice Pythonic way of visually grouping code into blocks that we want to happen atomically.

This pattern is so useful, in fact, that SQLAlchemy already uses a UoW in the shape of the `Session` object. The `Session` object in SQLAlchemy is the way that your application loads data from the database.

Every time you load a new entity from the database, the session begins to _track_ changes to the entity, and when the session is _flushed_, all your changes are persisted together. Why do we go to the effort of abstracting away the SQLAlchemy session if it already implements the pattern we want?

[Table 6-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#chapter_06_uow_tradeoffs) discusses some of the trade-offs.

Table 6-1. Unit of Work pattern: the trade-offs
|Pros|Cons|
|---|---|
|- We have a nice abstraction over the concept of atomic operations, and the context manager makes it easy to see, visually, what blocks of code are grouped together atomically.<br>    <br>- We have explicit control over when a transaction starts and finishes, and our application fails in a way that is safe by default. We never have to worry that an operation is partially committed.<br>    <br>- It’s a nice place to put all your repositories so client code can access them.<br>    <br>- As you’ll see in later chapters, atomicity isn’t only about transactions; it can help us work with events and the message bus.|- Your ORM probably already has some perfectly good abstractions around atomicity. SQLAlchemy even has context managers. You can go a long way just passing a session around.<br>    <br>- We’ve made it look easy, but you have to think quite carefully about things like rollbacks, multithreading, and nested transactions. Perhaps just sticking to what Django or Flask-SQLAlchemy gives you will keep your life simpler.|

For one thing, the Session API is rich and supports operations that we don’t want or need in our domain. Our `UnitOfWork` simplifies the session to its essential core: it can be started, committed, or thrown away.

For another, we’re using the `UnitOfWork` to access our `Repository` objects. This is a neat bit of developer usability that we couldn’t do with a plain SQLAlchemy `Session`.

##### UNIT OF WORK PATTERN RECAP

The Unit of Work pattern is an abstraction around data integrity

It helps to enforce the consistency of our domain model, and improves performance, by letting us perform a single _flush_ operation at the end of an operation.

It works closely with the Repository and Service Layer patterns

The Unit of Work pattern completes our abstractions over data access by representing atomic updates. Each of our service-layer use cases runs in a single unit of work that succeeds or fails as a block.

This is a lovely case for a context manager

Context managers are an idiomatic way of defining scope in Python. We can use a context manager to automatically roll back our work at the end of a request, which means the system is safe by default.

SQLAlchemy already implements this pattern

We introduce an even simpler abstraction over the SQLAlchemy `Session` object in order to “narrow” the interface between the ORM and our code. This helps to keep us loosely coupled.

Lastly, we’re motivated again by the dependency inversion principle: our service layer depends on a thin abstraction, and we attach a concrete implementation at the outside edge of the system. This lines up nicely with SQLAlchemy’s own [recommendations](https://oreil.ly/tS0E0):

> Keep the life cycle of the session (and usually the transaction) separate and external. The most comprehensive approach, recommended for more substantial applications, will try to keep the details of session, transaction, and exception management as far as possible from the details of the program doing its work.
> 
> SQLALchemy “Session Basics” Documentation

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#idm45846304958120-marker) You may have come across the use of the word _collaborators_ to describe objects that work together to achieve a goal. The unit of work and the repository are a great example of collaborators in the object-modeling sense. In responsibility-driven design, clusters of objects that collaborate in their roles are called _object neighborhoods_, which is, in our professional opinion, totally adorable.
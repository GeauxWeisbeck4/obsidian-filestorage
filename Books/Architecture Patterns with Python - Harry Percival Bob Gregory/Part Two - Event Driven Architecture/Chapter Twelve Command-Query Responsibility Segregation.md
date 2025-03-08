# Chapter 12. Command-Query Responsibility Segregation (CQRS)

In this chapter, we’re going to start with a fairly uncontroversial insight: reads (queries) and writes (commands) are different, so they should be treated differently (or have their responsibilities segregated, if you will). Then we’re going to push that insight as far as we can.

If you’re anything like Harry, this will all seem extreme at first, but hopefully we can make the argument that it’s not _totally_ unreasonable.

[Figure 12-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#maps_chapter_11) shows where we might end up.

###### TIP

The code for this chapter is in the chapter_12_cqrs branch [on GitHub](https://oreil.ly/YbWGT).

git clone https://github.com/cosmicpython/code.git
cd code
git checkout chapter_12_cqrs
# or to code along, checkout the previous chapter:
git checkout chapter_11_external_events

First, though, why bother?

![apwp 1201](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_1201.png)

###### Figure 12-1. Separating reads from writes

# Domain Models Are for Writing

We’ve spent a lot of time in this book talking about how to build software that enforces the rules of our domain. These rules, or constraints, will be different for every application, and they make up the interesting core of our systems.

In this book, we’ve set explicit constraints like “You can’t allocate more stock than is available,” as well as implicit constraints like “Each order line is allocated to a single batch.”

We wrote down these rules as unit tests at the beginning of the book:

_Our basic domain tests (tests/unit/test_batches.py)_

```
def
```

To apply these rules properly, we needed to ensure that operations were consistent, and so we introduced patterns like _Unit of Work_ and _Aggregate_ that help us commit small chunks of work.

To communicate changes between those small chunks, we introduced the Domain Events pattern so we can write rules like “When stock is damaged or lost, adjust the available quantity on the batch, and reallocate orders if necessary.”

All of this complexity exists so we can enforce rules when we change the state of our system. We’ve built a flexible set of tools for writing data.

What about reads, though?

# Most Users Aren’t Going to Buy Your Furniture

At MADE.com, we have a system very like the allocation service. In a busy day, we might process one hundred orders in an hour, and we have a big gnarly system for allocating stock to those orders.

In that same busy day, though, we might have one hundred product views per _second_. Each time somebody visits a product page, or a product listing page, we need to figure out whether the product is still in stock and how long it will take us to deliver it.

The _domain_ is the same—we’re concerned with batches of stock, and their arrival date, and the amount that’s still available—but the access pattern is very different. For example, our customers won’t notice if the query is a few seconds out of date, but if our allocate service is inconsistent, we’ll make a mess of their orders. We can take advantage of this difference by making our reads _eventually consistent_ in order to make them perform better.

##### IS READ CONSISTENCY TRULY ATTAINABLE?

This idea of trading consistency against performance makes a lot of developers nervous at first, so let’s talk quickly about that.

Let’s imagine that our “Get Available Stock” query is 30 seconds out of date when Bob visits the page for `ASYMMETRICAL-DRESSER`. Meanwhile, though, Harry has already bought the last item. When we try to allocate Bob’s order, we’ll get a failure, and we’ll need to either cancel his order or buy more stock and delay his delivery.

People who’ve worked only with relational data stores get _really_ nervous about this problem, but it’s worth considering two other scenarios to gain some perspective.

First, let’s imagine that Bob and Harry both visit the page at _the same time_. Harry goes off to make coffee, and by the time he returns, Bob has already bought the last dresser. When Harry places his order, we send it to the allocation service, and because there’s not enough stock, we have to refund his payment or buy more stock and delay his delivery.

As soon as we render the product page, the data is already stale. This insight is key to understanding why reads can be safely inconsistent: we’ll always need to check the current state of our system when we come to allocate, because all distributed systems are inconsistent. As soon as you have a web server and two customers, you have the potential for stale data.

OK, let’s assume we solve that problem somehow: we magically build a totally consistent web application where nobody ever sees stale data. This time Harry gets to the page first and buys his dresser.

Unfortunately for him, when the warehouse staff tries to dispatch his furniture, it falls off the forklift and smashes into a zillion pieces. Now what?

The only options are to either call Harry and refund his order or buy more stock and delay delivery.

No matter what we do, we’re always going to find that our software systems are inconsistent with reality, and so we’ll always need business processes to cope with these edge cases. It’s OK to trade performance for consistency on the read side, because stale data is essentially unavoidable.

We can think of these requirements as forming two halves of a system: the read side and the write side, shown in [Table 12-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#read_and_write_table).

For the write side, our fancy domain architectural patterns help us to evolve our system over time, but the complexity we’ve built so far doesn’t buy anything for reading data. The service layer, the unit of work, and the clever domain model are just bloat.

Table 12-1. Read versus write
||Read side|Write side|
|---|---|---|
|Behavior|Simple read|Complex business logic|
|Cacheability|Highly cacheable|Uncacheable|
|Consistency|Can be stale|Must be transactionally consistent|

# Post/Redirect/Get and CQS

If you do web development, you’re probably familiar with the Post/Redirect/Get pattern. In this technique, a web endpoint accepts an HTTP POST and responds with a redirect to see the result. For example, we might accept a POST to _/batches_ to create a new batch and redirect the user to _/batches/123_ to see their newly created batch.

This approach fixes the problems that arise when users refresh the results page in their browser or try to bookmark a results page. In the case of a refresh, it can lead to our users double-submitting data and thus buying two sofas when they needed only one. In the case of a bookmark, our hapless customers will end up with a broken page when they try to GET a POST endpoint.

Both these problems happen because we’re returning data in response to a write operation. Post/Redirect/Get sidesteps the issue by separating the read and write phases of our operation.

This technique is a simple example of command-query separation (CQS). In CQS we follow one simple rule: functions should either modify state or answer questions, but never both. This makes software easier to reason about: we should always be able to ask, “Are the lights on?” without flicking the light switch.

###### NOTE

When building APIs, we can apply the same design technique by returning a 201 Created, or a 202 Accepted, with a Location header containing the URI of our new resources. What’s important here isn’t the status code we use but the logical separation of work into a write phase and a query phase.

As you’ll see, we can use the CQS principle to make our systems faster and more scalable, but first, let’s fix the CQS violation in our existing code. Ages ago, we introduced an `allocate` endpoint that takes an order and calls our service layer to allocate some stock. At the end of the call, we return a 200 OK and the batch ID. That’s led to some ugly design flaws so that we can get the data we need. Let’s change it to return a simple OK message and instead provide a new read-only endpoint to retrieve allocation state:

_API test does a GET after the POST (tests/e2e/test_api.py)_

```
@pytest.mark.usefixtures
```

OK, what might the Flask app look like?

_Endpoint for viewing allocations (src/allocation/entrypoints/flask_app.py)_

```
from
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#co_command_query_responsibility_segregation__cqrs__CO1-1)

All right, a _views.py_, fair enough; we can keep read-only stuff in there, and it’ll be a real _views.py_, not like Django’s, something that knows how to build read-only views of our data…

# Hold On to Your Lunch, Folks

Hmm, so we can probably just add a list method to our existing repository object:

_Views do…raw SQL? (src/allocation/views.py)_

```
from
```

_Excuse me? Raw SQL?_

If you’re anything like Harry encountering this pattern for the first time, you’ll be wondering what on earth Bob has been smoking. We’re hand-rolling our own SQL now, and converting database rows directly to dicts? After all the effort we put into building a nice domain model? And what about the Repository pattern? Isn’t that meant to be our abstraction around the database? Why don’t we reuse that?

Well, let’s explore that seemingly simpler alternative first, and see what it looks like in practice.

We’ll still keep our view in a separate _views.py_ module; enforcing a clear distinction between reads and writes in your application is still a good idea. We apply command-query separation, and it’s easy to see which code modifies state (the event handlers) and which code just retrieves read-only state (the views).

###### TIP

Splitting out your read-only views from your state-modifying command and event handlers is probably a good idea, even if you don’t want to go to full-blown CQRS.

# Testing CQRS Views

Before we get into exploring various options, let’s talk about testing. Whichever approaches you decide to go for, you’re probably going to need at least one integration test. Something like this:

_An integration test for a view (tests/integration/test_views.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#co_command_query_responsibility_segregation__cqrs__CO2-1)

We do the setup for the integration test by using the public entrypoint to our application, the message bus. That keeps our tests decoupled from any implementation/infrastructure details about how things get stored.

# “Obvious” Alternative 1: Using the Existing Repository

How about adding a helper method to our `products` repository?

_A simple view that uses the repository (src/allocation/views.py)_

```
from
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#co_command_query_responsibility_segregation__cqrs__CO3-1)

Our repository returns `Product` objects, and we need to find all the products for the SKUs in a given order, so we’ll build a new helper method called `.for_order()` on the repository.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#co_command_query_responsibility_segregation__cqrs__CO3-2)

Now we have products but we actually want batch references, so we get all the possible batches with a list comprehension.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#co_command_query_responsibility_segregation__cqrs__CO3-3)

We filter _again_ to get just the batches for our specific order. That, in turn, relies on our `Batch` objects being able to tell us which order IDs it has allocated.

We implement that last using a `.orderid` property:

_An arguably unnecessary property on our model (src/allocation/domain/model.py)_

```
class
```

You can start to see that reusing our existing repository and domain model classes is not as straightforward as you might have assumed. We’ve had to add new helper methods to both, and we’re doing a bunch of looping and filtering in Python, which is work that would be done much more efficiently by the database.

So yes, on the plus side we’re reusing our existing abstractions, but on the downside, it all feels quite clunky.

# Your Domain Model Is Not Optimized for Read Operations

What we’re seeing here are the effects of having a domain model that is designed primarily for write operations, while our requirements for reads are often conceptually quite different.

This is the chin-stroking-architect’s justification for CQRS. As we’ve said before, a domain model is not a data model—we’re trying to capture the way the business works: workflow, rules around state changes, messages exchanged; concerns about how the system reacts to external events and user input. _Most of this stuff is totally irrelevant for read-only operations_.

###### TIP

This justification for CQRS is related to the justification for the Domain Model pattern. If you’re building a simple CRUD app, reads and writes are going to be closely related, so you don’t need a domain model or CQRS. But the more complex your domain, the more likely you are to need both.

To make a facile point, your domain classes will have multiple methods for modifying state, and you won’t need any of them for read-only operations.

As the complexity of your domain model grows, you will find yourself making more and more choices about how to structure that model, which make it more and more awkward to use for read operations.

# “Obvious” Alternative 2: Using the ORM

You may be thinking, OK, if our repository is clunky, and working with `Products` is clunky, then I can at least use my ORM and work with `Batches`. That’s what it’s for!

_A simple view that uses the ORM (src/allocation/views.py)_

```
from
```

But is that _actually_ any easier to write or understand than the raw SQL version from the code example in [“Hold On to Your Lunch, Folks”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#hold-on-ch12)? It may not look too bad up there, but we can tell you it took several attempts, and plenty of digging through the SQLAlchemy docs. SQL is just SQL.

But the ORM can also expose us to performance problems.

# SELECT N+1 and Other Performance Considerations

The so-called [`SELECT N+1`](https://oreil.ly/OkBOS) problem is a common performance problem with ORMs: when retrieving a list of objects, your ORM will often perform an initial query to, say, get all the IDs of the objects it needs, and then issue individual queries for each object to retrieve their attributes. This is especially likely if there are any foreign-key relationships on your objects.

###### NOTE

In all fairness, we should say that SQLAlchemy is quite good at avoiding the `SELECT N+1` problem. It doesn’t display it in the preceding example, and you can request [eager loading](https://oreil.ly/XKDDm) explicitly to avoid it when dealing with joined objects.

Beyond `SELECT N+1`, you may have other reasons for wanting to decouple the way you persist state changes from the way that you retrieve current state. A set of fully normalized relational tables is a good way to make sure that write operations never cause data corruption. But retrieving data using lots of joins can be slow. It’s common in such cases to add some denormalized views, build read replicas, or even add caching layers.

# Time to Completely Jump the Shark

On that note: have we convinced you that our raw SQL version isn’t so weird as it first seemed? Perhaps we were exaggerating for effect? Just you wait.

So, reasonable or not, that hardcoded SQL query is pretty ugly, right? What if we made it nicer…

_A much nicer query (src/allocation/views.py)_

```
def
```

…by _keeping a totally separate, denormalized data store for our view model_?

_Hee hee hee, no foreign keys, just strings, YOLO (src/allocation/adapters/orm.py)_

```
allocations_view
```

OK, nicer-looking SQL queries wouldn’t be a justification for anything really, but building a denormalized copy of your data that’s optimized for read operations isn’t uncommon, once you’ve reached the limits of what you can do with indexes.

Even with well-tuned indexes, a relational database uses a lot of CPU to perform joins. The fastest queries will always be `SELECT * from _mytable_ WHERE _key_ = :_value_`.

More than raw speed, though, this approach buys us scale. When we’re writing data to a relational database, we need to make sure that we get a lock over the rows we’re changing so we don’t run into consistency problems.

If multiple clients are changing data at the same time, we’ll have weird race conditions. When we’re _reading_ data, though, there’s no limit to the number of clients that can concurrently execute. For this reason, read-only stores can be horizontally scaled out.

###### TIP

Because read replicas can be inconsistent, there’s no limit to how many we can have. If you’re struggling to scale a system with a complex data store, ask whether you could build a simpler read model.

Keeping the read model up to date is the challenge! Database views (materialized or otherwise) and triggers are a common solution, but that limits you to your database. We’d like to show you how to reuse our event-driven architecture instead.

## Updating a Read Model Table Using an Event Handler

We add a second handler to the `Allocated` event:

_Allocated event gets a new handler (src/allocation/service_layer/messagebus.py)_

```
EVENT_HANDLERS
```

Here’s what our update-view-model code looks like:

_Update on allocation (src/allocation/service_layer/handlers.py)_

```
def
```

Believe it or not, that will pretty much work! _And it will work against the exact same integration tests as the rest of our options._

OK, you’ll also need to handle `Deallocated`:

_A second listener for read model updates_

```
events
```

[Figure 12-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#read_model_sequence_diagram) shows the flow across the two requests.

![apwp 1202](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_1202.png)

###### Figure 12-2. Sequence diagram for read model

In [Figure 12-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#read_model_sequence_diagram), you can see two transactions in the POST/write operation, one to update the write model and one to update the read model, which the GET/read operation can use.

##### REBUILDING FROM SCRATCH

“What happens when it breaks?” should be the first question we ask as engineers.

How do we deal with a view model that hasn’t been updated because of a bug or temporary outage? Well, this is just another case where events and commands can fail independently.

If we _never_ updated the view model, and the `ASYMMETRICAL-DRESSER` was forever in stock, that would be annoying for customers, but the `allocate` service would still fail, and we’d take action to fix the problem.

Rebuilding a view model is easy, though. Since we’re using a service layer to update our view model, we can write a tool that does the following:

- Queries the current state of the write side to work out what’s currently allocated
    
- Calls the `add_allocate_to_read_model` handler for each allocated item
    

We can use this technique to create entirely new read models from historical data.

# Changing Our Read Model Implementation Is Easy

Let’s see the flexibility that our event-driven model buys us in action, by seeing what happens if we ever decide we want to implement a read model by using a totally separate storage engine, Redis.

Just watch:

_Handlers update a Redis read model (src/allocation/service_layer/handlers.py)_

```
def
```

The helpers in our Redis module are one-liners:

_Redis read model read and update (src/allocation/adapters/redis_eventpublisher.py)_

```
def
```

(Maybe the name _redis_eventpublisher.py_ is a misnomer now, but you get the idea.)

And the view itself changes very slightly to adapt to its new backend:

_View adapted to Redis (src/allocation/views.py)_

```
def
```

And the _exact same_ integration tests that we had before still pass, because they are written at a level of abstraction that’s decoupled from the implementation: setup puts messages on the message bus, and the assertions are against our view.

###### TIP

Event handlers are a great way to manage updates to a read model, if you decide you need one. They also make it easy to change the implementation of that read model at a later date.

##### EXERCISE FOR THE READER

Implement another view, this time to show the allocation for a single order line.

Here the trade-offs between using hardcoded SQL versus going via a repository should be much more blurry. Try a few versions (maybe including going to Redis), and see which you prefer.

# Wrap-Up

[Table 12-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#view_model_tradeoffs) proposes some pros and cons for each of our options.

As it happens, the allocation service at MADE.com does use “full-blown” CQRS, with a read model stored in Redis, and even a second layer of cache provided by Varnish. But its use cases are quite a bit different from what we’ve shown here. For the kind of allocation service we’re building, it seems unlikely that you’d need to use a separate read model and event handlers for updating it.

But as your domain model becomes richer and more complex, a simplified read model become ever more compelling.

Table 12-2. Trade-offs of various view model options
|Option|Pros|Cons|
|---|---|---|
|Just use repositories|Simple, consistent approach.|Expect performance issues with complex query patterns.|
|Use custom queries with your ORM|Allows reuse of DB configuration and model definitions.|Adds another query language with its own quirks and syntax.|
|Use hand-rolled SQL|Offers fine control over performance with a standard query syntax.|Changes to DB schema have to be made to your hand-rolled queries _and_ your ORM definitions. Highly normalized schemas may still have performance limitations.|
|Create separate read stores with events|Read-only copies are easy to scale out. Views can be constructed when data changes so that queries are as simple as possible.|Complex technique. Harry will be forever suspicious of your tastes and motives.|

Often, your read operations will be acting on the same conceptual objects as your write model, so using the ORM, adding some read methods to your repositories, and using domain model classes for your read operations is _just fine_.

In our book example, the read operations act on quite different conceptual entities to our domain model. The allocation service thinks in terms of `Batches` for a single SKU, but users care about allocations for a whole order, with multiple SKUs, so using the ORM ends up being a little awkward. We’d be quite tempted to go with the raw-SQL view we showed right at the beginning of the chapter.

On that note, let’s sally forth into our final chapter.
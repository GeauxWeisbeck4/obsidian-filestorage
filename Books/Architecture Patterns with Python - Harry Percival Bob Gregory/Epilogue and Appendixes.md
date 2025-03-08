# Epilogue

# What Now?

Phew! We’ve covered a lot of ground in this book, and for most of our audience all of these ideas are new. With that in mind, we can’t hope to make you experts in these techniques. All we can really do is show you the broad-brush ideas, and just enough code for you to go ahead and write something from scratch.

The code we’ve shown in this book isn’t battle-hardened production code: it’s a set of Lego blocks that you can play with to make your first house, spaceship, and skyscraper.

That leaves us with two big tasks. We want to talk about how to start applying these ideas for real in an existing system, and we need to warn you about some of the things we had to skip. We’ve given you a whole new arsenal of ways to shoot yourself in the foot, so we should discuss some basic firearms safety.

# How Do I Get There from Here?

Chances are that a lot of you are thinking something like this:

“OK Bob and Harry, that’s all well and good, and if I ever get hired to work on a green-field new service, I know what to do. But in the meantime, I’m here with my big ball of Django mud, and I don’t see any way to get to your nice, clean, perfect, untainted, simplistic model. Not from here.”

We hear you. Once you’ve already _built_ a big ball of mud, it’s hard to know how to start improving things. Really, we need to tackle things step by step.

First things first: what problem are you trying to solve? Is the software too hard to change? Is the performance unacceptable? Have you got weird, inexplicable bugs?

Having a clear goal in mind will help you to prioritize the work that needs to be done and, importantly, communicate the reasons for doing it to the rest of the team. Businesses tend to have pragmatic approaches to technical debt and refactoring, so long as engineers can make a reasoned argument for fixing things.

###### TIP

Making complex changes to a system is often an easier sell if you link it to feature work. Perhaps you’re launching a new product or opening your service to new markets? This is the right time to spend engineering resources on fixing the foundations. With a six-month project to deliver, it’s easier to make the argument for three weeks of cleanup work. Bob refers to this as _architecture tax_.

# Separating Entangled Responsibilities

At the beginning of the book, we said that the main characteristic of a big ball of mud is homogeneity: every part of the system looks the same, because we haven’t been clear about the responsibilities of each component. To fix that, we’ll need to start separating out responsibilities and introducing clear boundaries. One of the first things we can do is to start building a service layer ([Figure E-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#collaboration_app_model)).

![apwp ep01](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_ep01.png)

###### Figure E-1. Domain of a collaboration system

This was the system in which Bob first learned how to break apart a ball of mud, and it was a doozy. There was logic _everywhere_—in the web pages, in manager objects, in helpers, in fat service classes that we’d written to abstract the managers and helpers, and in hairy command objects that we’d written to break apart the services.

If you’re working in a system that’s reached this point, the situation can feel hopeless, but it’s never too late to start weeding an overgrown garden. Eventually, we hired an architect who knew what he was doing, and he helped us get things back under control.

Start by working out the _use cases_ of your system. If you have a user interface, what actions does it perform? If you have a backend processing component, maybe each cron job or Celery job is a single use case. Each of your use cases needs to have an imperative name: Apply Billing Charges, Clean Abandoned Accounts, or Raise Purchase Order, for example.

In our case, most of our use cases were part of the manager classes and had names like Create Workspace or Delete Document Version. Each use case was invoked from a web frontend.

We aim to create a single function or class for each of these supported operations that deals with _orchestrating_ the work to be done. Each use case should do the following:

- Start its own database transaction if needed
    
- Fetch any required data
    
- Check any preconditions (see the Ensure pattern in [Appendix E](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#appendix_validation))
    
- Update the domain model
    
- Persist any changes
    

Each use case should succeed or fail as an atomic unit. You might need to call one use case from another. That’s OK; just make a note of it, and try to avoid long-running database transactions.

###### NOTE

One of the biggest problems we had was that manager methods called other manager methods, and data access could happen from the model objects themselves. It was hard to understand what each operation did without going on a treasure hunt across the codebase. Pulling all the logic into a single method, and using a UoW to control our transactions, made the system easier to reason about.

##### CASE STUDY: LAYERING AN OVERGROWN SYSTEM

Many years ago, Bob worked for a software company that had outsourced the first version of its application, an online collaboration platform for sharing and working on files.

When the company brought development in-house, it passed through several generations of developers’ hands, and each wave of new developers added more complexity to the code’s structure.

At its heart, the system was an ASP.NET Web Forms application, built with an NHibernate ORM. Users would upload documents into workspaces, where they could invite other workspace members to review, comment on, or modify their work.

Most of the complexity of the application was in the permissions model because each document was contained in a folder, and folders allowed read, write, and edit permissions, much like a Linux filesystem.

Additionally, each workspace belonged to an account, and the account had quotas attached to it via a billing package.

As a result, every read or write operation against a document had to load an enormous number of objects from the database in order to test permissions and quotas. Creating a new workspace involved hundreds of database queries as we set up the permissions structure, invited users, and set up sample content.

Some of the code for operations was in web handlers that ran when a user clicked a button or submitted a form; some of it was in manager objects that held code for orchestrating work; and some of it was in the domain model. Model objects would make database calls or copy files on disk, and the test coverage was abysmal.

To fix the problem, we first introduced a service layer so that all of the code for creating a document or workspace was in one place and could be understood. This involved pulling data access code out of the domain model and into command handlers. Likewise, we pulled orchestration code out of the managers and the web handlers and pushed it into handlers.

The resulting command handlers were _long_ and messy, but we’d made a start at introducing order to the chaos.

###### TIP

It’s fine if you have duplication in the use-case functions. We’re not trying to write perfect code; we’re just trying to extract some meaningful layers. It’s better to duplicate some code in a few places than to have use-case functions calling one another in a long chain.

This is a good opportunity to pull any data-access or orchestration code out of the domain model and into the use cases. We should also try to pull I/O concerns (e.g., sending email, writing files) out of the domain model and up into the use-case functions. We apply the techniques from [Chapter 3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#chapter_03_abstractions) on abstractions to keep our handlers unit testable even when they’re performing I/O.

These use-case functions will mostly be about logging, data access, and error handling. Once you’ve done this step, you’ll have a grasp of what your program actually _does_, and a way to make sure each operation has a clearly defined start and finish. We’ll have taken a step toward building a pure domain model.

Read _Working Effectively with Legacy Code_ by Michael C. Feathers (Prentice Hall) for guidance on getting legacy code under test and starting separating responsibilities.

# Identifying Aggregates and Bounded Contexts

Part of the problem with the codebase in our case study was that the object graph was highly connected. Each account had many workspaces, and each workspace had many members, all of whom had their own accounts. Each workspace contained many documents, which had many versions.

You can’t express the full horror of the thing in a class diagram. For one thing, there wasn’t really a single account related to a user. Instead, there was a bizarre rule requiring you to enumerate all of the accounts associated to the user via the workspaces and take the one with the earliest creation date.

Every object in the system was part of an inheritance hierarchy that included `SecureObject` and `Version`. This inheritance hierarchy was mirrored directly in the database schema, so that every query had to join across 10 different tables and look at a discriminator column just to tell what kind of objects you were working with.

The codebase made it easy to “dot” your way through these objects like so:

```
user
```

Building a system this way with Django ORM or SQLAlchemy is easy but is to be avoided. Although it’s _convenient_, it makes it very hard to reason about performance because each property might trigger a lookup to the database.

###### TIP

Aggregates are a _consistency boundary_. In general, each use case should update a single aggregate at a time. One handler fetches one aggregate from a repository, modifies its state, and raises any events that happen as a result. If you need data from another part of the system, it’s totally fine to use a read model, but avoid updating multiple aggregates in a single transaction. When we choose to separate code into different aggregates, we’re explicitly choosing to make them _eventually consistent_ with one another.

A bunch of operations required us to loop over objects this way—for example:

```
# Lock a user's workspaces for nonpayment
```

Or even recurse over collections of folders and documents:

```
def
```

These operations _killed_ performance, but fixing them meant giving up our single object graph. Instead, we began to identify aggregates and to break the direct links between objects.

###### NOTE

We talked about the infamous `SELECT N+1` problem in [Chapter 12](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch12.html#chapter_12_cqrs), and how we might choose to use different techniques when reading data for queries versus reading data for commands.

Mostly we did this by replacing direct references with identifiers.

Before aggregates:

![apwp ep02](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_ep02.png)

After modeling with aggregates:

![apwp ep03](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_ep03.png)

###### TIP

Bidirectional links are often a sign that your aggregates aren’t right. In our original code, a `Document` knew about its containing `Folder`, and the `Folder` had a collection of `Documents`. This makes it easy to traverse the object graph but stops us from thinking properly about the consistency boundaries we need. We break apart aggregates by using references instead. In the new model, a `Document` had reference to its `parent_folder` but had no way to directly access the `Folder`.

If we needed to _read_ data, we avoided writing complex loops and transforms and tried to replace them with straight SQL. For example, one of our screens was a tree view of folders and documents.

This screen was _incredibly_ heavy on the database, because it relied on nested `for` loops that triggered a lazy-loaded ORM.

###### TIP

We use this same technique in [Chapter 11](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch11.html#chapter_11_external_events), where we replace a nested loop over ORM objects with a simple SQL query. It’s the first step in a CQRS approach.

After a lot of head-scratching, we replaced the ORM code with a big, ugly stored procedure. The code looked horrible, but it was much faster and helped to break the links between `Folder` and `Document`.

When we needed to _write_ data, we changed a single aggregate at a time, and we introduced a message bus to handle events. For example, in the new model, when we locked an account, we could first query for all the affected workspaces via `SELECT _id_ FROM _workspace_ WHERE _account_id_ = ?`.

We could then raise a new command for each workspace:

```
for
```

# An Event-Driven Approach to Go to Microservices via Strangler Pattern

The _Strangler Fig_ pattern involves creating a new system around the edges of an old system, while keeping it running. Bits of old functionality are gradually intercepted and replaced, until the old system is left doing nothing at all and can be switched off.

When building the availability service, we used a technique called _event interception_ to move functionality from one place to another. This is a three-step process:

1. Raise events to represent the changes happening in a system you want to replace.
    
2. Build a second system that consumes those events and uses them to build its own domain model.
    
3. Replace the older system with the new.
    

We used event interception to move from [Figure E-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#strangler_before)…

![apwp ep04](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_ep04.png)

###### Figure E-2. Before: strong, bidirectional coupling based on XML-RPC

to [Figure E-3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#strangler_after).

![apwp ep05](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_ep05.png)

###### Figure E-3. After: loose coupling with asynchronous events (you can find a high-resolution version of this diagram at cosmicpython.com)

Practically, this was a several month-long project. Our first step was to write a domain model that could represent batches, shipments, and products. We used TDD to build a toy system that could answer a single question: “If I want N units of HAZARDOUS_RUG, how long will they take to be delivered?”

###### TIP

When deploying an event-driven system, start with a “walking skeleton.” Deploying a system that just logs its input forces us to tackle all the infrastructural questions and start working in production.

##### CASE STUDY: CARVING OUT A MICROSERVICE TO REPLACE A DOMAIN

MADE.com started out with _two_ monoliths: one for the frontend ecommerce application, and one for the backend fulfillment system.

The two systems communicated through XML-RPC. Periodically, the backend system would wake up and query the frontend system to find out about new orders. When it had imported all the new orders, it would send RPC commands to update the stock levels.

Over time this synchronization process became slower and slower until, one Christmas, it took longer than 24 hours to import a single day’s orders. Bob was hired to break the system into a set of event-driven services.

First, we identified that the slowest part of the process was calculating and synchronizing the available stock. What we needed was a system that could listen to external events and keep a running total of how much stock was available.

We exposed that information via an API, so that the user’s browser could ask how much stock was available for each product and how long it would take to deliver to their address.

Whenever a product ran out of stock completely, we would raise a new event that the ecommerce platform could use to take a product off sale. Because we didn’t know how much load we would need to handle, we wrote the system with a CQRS pattern. Whenever the amount of stock changed, we would update a Redis database with a cached view model. Our Flask API queried these _view models_ instead of running the complex domain model.

As a result, we could answer the question “How much stock is available?” in 2 to 3 milliseconds, and now the API frequently handles hundreds of requests a second for sustained periods.

If this all sounds a little familiar, well, now you know where our example app came from!

Once we had a working domain model, we switched to building out some infrastructural pieces. Our first production deployment was a tiny system that could receive a `batch_created` event and log its JSON representation. This is the “Hello World” of event-driven architecture. It forced us to deploy a message bus, hook up a producer and consumer, build a deployment pipeline, and write a simple message handler.

Given a deployment pipeline, the infrastructure we needed, and a basic domain model, we were off. A couple months later, we were in production and serving real customers.

# Convincing Your Stakeholders to Try Something New

If you’re thinking about carving a new system out of a big ball of mud, you’re probably suffering problems with reliability, performance, maintainability, or all three simultaneously. Deep, intractable problems call for drastic measures!

We recommend _domain modeling_ as a first step. In many overgrown systems, the engineers, product owners, and customers no longer speak the same language. Business stakeholders speak about the system in abstract, process-focused terms, while developers are forced to speak about the system as it physically exists in its wild and chaotic state.

##### CASE STUDY: THE USER MODEL

We mentioned earlier that the account and user model in our first system were bound together by a “bizarre rule.” This is a perfect example of how engineering and business stakeholders can drift apart.

In this system, _accounts_ parented _workspaces_, and users were _members_ of workspaces. Workspaces were the fundamental unit for applying permissions and quotas. If a user _joined_ a workspace and didn’t already have an _account_, we would associate them with the account that owned that workspace.

This was messy and ad hoc, but it worked fine until the day a product owner asked for a new feature:

> When a user joins a company, we want to add them to some default workspaces for the company, like the HR workspace or the Company Announcements workspace.

We had to explain to them that there was _no such thing_ as a company, and there was no sense in which a user joined an account. Moreover, a “company” might have _many_ accounts owned by different users, and a new user might be invited to any one of them.

Years of adding hacks and work-arounds to a broken model caught up with us, and we had to rewrite the entire user management function as a brand-new system.

Figuring out how to model your domain is a complex task that’s the subject of many decent books in its own right. We like to use interactive techniques like event storming and CRC modeling, because humans are good at collaborating through play. _Event modeling_ is another technique that brings engineers and product owners together to understand a system in terms of commands, queries, and events.

###### TIP

Check out _www.eventmodeling.org_ and _www.eventstorming.org_ for some great guides to visual modeling of systems with events.

The goal is to be able to talk about the system by using the same ubiquitous language, so that you can agree on where the complexity lies.

We’ve found a lot of value in treating domain problems as TDD kata. For example, the first code we wrote for the availability service was the batch and order line model. You can treat this as a lunchtime workshop, or as a spike at the beginning of a project. Once you can demonstrate the value of modeling, it’s easier to make the argument for structuring the project to optimize for modeling.

##### CASE STUDY: DAVID SEDDON ON TAKING SMALL STEPS

_Hi, I’m David, one of the tech reviewers on this book. I’ve worked on several complex Django monoliths, and so I’ve known the pain that Bob and Harry have made all sorts of grand promises about soothing._

_When I was first exposed to the patterns described here, I was rather excited. I had successfully used some of the techniques already on smaller projects, but here was a blueprint for much larger, database-backed systems like the one I work on in my day job. So I started trying to figure out how I could implement that blueprint at my current organization._

_I chose to tackle a problem area of the codebase that had always bothered me. I began by implementing it as a use case. But I found myself running into unexpected questions. There were things that I hadn’t considered while reading that now made it difficult to see what to do. Was it a problem if my use case interacted with two different aggregates? Could one use case call another? And how was it going to exist within a system that followed different architectural principles without resulting in a horrible mess?_

_What happened to that oh-so-promising blueprint? Did I actually understand the ideas well enough to put them into practice? Was it even suitable for my application? Even if it was, would any of my colleagues agree to such a major change? Were these just nice ideas for me to fantasize about while I got on with real life?_

_It took me a while to realize that I could start small. I didn’t need to be a purist or to _get it right_ the first time: I could experiment, finding what worked for me._

_And so that’s what I’ve done. I’ve been able to apply_ some _of the ideas in a few places. I’ve built new features whose business logic can be tested without the database or mocks. And as a team, we’ve introduced a service layer to help define the jobs the system does._

_If you start trying to apply these patterns in your work, you may go through similar feelings to begin with. When the nice theory of a book meets the reality of your codebase, it can be demoralizing._

_My advice is to focus on a specific problem and ask yourself how you can put the relevant ideas to use, perhaps in an initially limited and imperfect fashion. You may discover, as I did, that the first problem you pick might be a bit too difficult; if so, move on to something else. Don’t try to boil the ocean, and don’t be_ too _afraid of making mistakes. It will be a learning experience, and you can be confident that you’re moving roughly in a direction that others have found useful._

_So, if you’re feeling the pain too, give these ideas a try. Don’t feel you need permission to rearchitect everything. Just look for somewhere small to start. And above all, do it to solve a specific problem. If you’re successful in solving it, you’ll know you got something right—and others will too._

# Questions Our Tech Reviewers Asked That We Couldn’t Work into Prose

Here are some questions we heard during drafting that we couldn’t find a good place to address elsewhere in the book:

Do I need to do all of this at once? Can I just do a bit at a time?

No, you can absolutely adopt these techniques bit by bit. If you have an existing system, we recommend building a service layer to try to keep orchestration in one place. Once you have that, it’s much easier to push logic into the model and push edge concerns like validation or error handling to the entrypoints.

It’s worth having a service layer even if you still have a big, messy Django ORM because it’s a way to start understanding the boundaries of operations.

Extracting use cases will break a lot of my existing code; it’s too tangled

Just copy and paste. It’s OK to cause more duplication in the short term. Think of this as a multistep process. Your code is in a bad state now, so copy and paste it to a new place and then make that new code clean and tidy.

Once you’ve done that, you can replace uses of the old code with calls to your new code and finally delete the mess. Fixing large codebases is a messy and painful process. Don’t expect things to get instantly better, and don’t worry if some bits of your application stay messy.

Do I need to do CQRS? That sounds weird. Can’t I just use repositories?

Of course you can! The techniques we’re presenting in this book are intended to make your life _easier_. They’re not some kind of ascetic discipline with which to punish yourself.

In our first case-study system, we had a lot of _View Builder_ objects that used repositories to fetch data and then performed some transformations to return dumb read models. The advantage is that when you hit a performance problem, it’s easy to rewrite a view builder to use custom queries or raw SQL.

How should use cases interact across a larger system? Is it a problem for one to call another?

This might be an interim step. Again, in the first case study, we had handlers that would need to invoke other handlers. This gets _really_ messy, though, and it’s much better to move to using a message bus to separate these concerns.

Generally, your system will have a single message bus implementation and a bunch of subdomains that center on a particular aggregate or set of aggregates. When your use case has finished, it can raise an event, and a handler elsewhere can run.

Is it a code smell for a use case to use multiple repositories/aggregates, and if so, why?

An aggregate is a consistency boundary, so if your use case needs to update two aggregates atomically (within the same transaction), then your consistency boundary is wrong, strictly speaking. Ideally you should think about moving to a new aggregate that wraps up all the things you want to change at the same time.

If you’re actually updating only one aggregate and using the other(s) for read-only access, then that’s _fine_, although you could consider building a read/view model to get you that data instead—it makes things cleaner if each use case has only one aggregate.

If you do need to modify two aggregates, but the two operations don’t have to be in the same transaction/UoW, then consider splitting the work out into two different handlers and using a domain event to carry information between the two. You can read more in [these papers on aggregate design](https://oreil.ly/sufKE) by Vaughn Vernon.

What if I have a read-only but business-logic-heavy system?

View models can have complex logic in them. In this book, we’ve encouraged you to separate your read and write models because they have different consistency and throughput requirements. Mostly, we can use simpler logic for reads, but that’s not always true. In particular, permissions and authorization models can add a lot of complexity to our read side.

We’ve written systems in which the view models needed extensive unit tests. In those systems, we split a _view builder_ from a _view fetcher_, as in [Figure E-4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#view_builder_diagram).

![apwp ep06](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_ep06.png)

###### Figure E-4. A view builder and view fetcher (you can find a high-resolution version of this diagram at cosmicpython.com)

+ This makes it easy to test the view builder by giving it mocked data (e.g., a list of dicts). “Fancy CQRS” with event handlers is really a way of running our complex view logic whenever we write so that we can avoid running it when we read.

Do I need to build microservices to do this stuff?

Egads, no! These techniques predate microservices by a decade or so. Aggregates, domain events, and dependency inversion are ways to control complexity in large systems. It just so happens that when you’ve built a set of use cases and a model for a business process, moving it to its own service is relatively easy, but that’s not a requirement.

I’m using Django. Can I still do this?

We have an entire appendix just for you: [Appendix D](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#appendix_django)!

# Footguns

OK, so we’ve given you a whole bunch of new toys to play with. Here’s the fine print. Harry and Bob do not recommend that you copy and paste our code into a production system and rebuild your automated trading platform on Redis pub/sub. For reasons of brevity and simplicity, we’ve hand-waved a lot of tricky subjects. Here’s a list of things we think you should know before trying this for real.

Reliable messaging is hard

Redis pub/sub is not reliable and shouldn’t be used as a general-purpose messaging tool. We picked it because it’s familiar and easy to run. At MADE, we run Event Store as our messaging tool, but we’ve had experience with RabbitMQ and Amazon EventBridge.

Tyler Treat has some excellent blog posts on his site _bravenewgeek.com_; you should read at least read [“You Cannot Have Exactly-Once Delivery”](https://oreil.ly/pcstD) and [“What You Want Is What You Don’t: Understanding Trade-Offs in Distributed Messaging”](https://oreil.ly/j8bmF).

We explicitly choose small, focused transactions that can fail independently

In [Chapter 8](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch08.html#chapter_08_events_and_message_bus), we update our process so that _deallocating_ an order line and _reallocating_ the line happen in two separate units of work. You will need monitoring to know when these transactions fail, and tooling to replay events. Some of this is made easier by using a transaction log as your message broker (e.g., Kafka or EventStore). You might also look at the [Outbox pattern](https://oreil.ly/sLfnp).

We don’t discuss idempotency

We haven’t given any real thought to what happens when handlers are retried. In practice you will want to make handlers idempotent so that calling them repeatedly with the same message will not make repeated changes to state. This is a key technique for building reliability, because it enables us to safely retry events when they fail.

There’s a lot of good material on idempotent message handling, try starting with [“How to Ensure Idempotency in an Eventual Consistent DDD/CQRS Application”](https://oreil.ly/yERzR) and [“(Un)Reliability in Messaging”](https://oreil.ly/Ekuhi).

Your events will need to change their schema over time

You’ll need to find some way of documenting your events and sharing schema with consumers. We like using JSON schema and markdown because it’s simple but there is other prior art. Greg Young wrote an entire book on managing event-driven systems over time: _Versioning in an Event Sourced System_ (Leanpub).

# More Required Reading

A few more books we’d like to recommend to help you on your way:

- _Clean Architectures in Python_ by Leonardo Giordani (Leanpub), which came out in 2019, is one of the few previous books on application architecture in Python.
    
- _Enterprise Integration Patterns_ by Gregor Hohpe and Bobby Woolf (Addison-Wesley Professional) is a pretty good start for messaging patterns.
    
- _Monolith to Microservices_ by Sam Newman (O’Reilly), and Newman’s first book, _Building Microservices_ (O’Reilly). The Strangler Fig pattern is mentioned as a favorite, along with many others. These are good to check out if you’re thinking of moving to microservices, and they’re also good on integration patterns and the considerations of async messaging-based integration.
    

# Wrap-Up

Phew! That’s a lot of warnings and reading suggestions; we hope we haven’t scared you off completely. Our goal with this book is to give you just enough knowledge and intuition for you to start building some of this for yourself. We would love to hear how you get on and what problems you’re facing with the techniques in your own systems, so why not get in touch with us over at _www.cosmicpython.com_?

# Appendix A. Summary Diagram and Table

Here’s what our architecture looks like by the end of the book:

![diagram showing all components: flask+eventconsumer, service layer, adapters, domain etc](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_aa01.png)

[Table A-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app01.html#ds1_table) recaps each pattern and what it does.

Table A-1. The components of our architecture and what they all do
|Layer|Component|Description|
|---|---|---|
|**Domain**<br><br>_Defines the business logic._|Entity|A domain object whose attributes may change but that has a recognizable identity over time.|
|Value object|An immutable domain object whose attributes entirely define it. It is fungible with other identical objects.|
|Aggregate|Cluster of associated objects that we treat as a unit for the purpose of data changes. Defines and enforces a consistency boundary.|
|Event|Represents something that happened.|
|Command|Represents a job the system should perform.|
|**Service Layer**<br><br>_Defines the jobs the system should perform and orchestrates different components._|Handler|Receives a command or an event and performs what needs to happen.|
|Unit of work|Abstraction around data integrity. Each unit of work represents an atomic update. Makes repositories available. Tracks new events on retrieved aggregates.|
|Message bus (internal)|Handles commands and events by routing them to the appropriate handler.|
|**Adapters** (Secondary)<br><br>_Concrete implementations of an interface that goes from our system to the outside world (I/O)._|Repository|Abstraction around persistent storage. Each aggregate has its own repository.|
|Event publisher|Pushes events onto the external message bus.|
|**Entrypoints** (Primary adapters)<br><br>_Translate external inputs into calls into the service layer._|Web|Receives web requests and translates them into commands, passing them to the internal message bus.|
|Event consumer|Reads events from the external message bus and translates them into commands, passing them to the internal message bus.|
|N/A|External message bus (message broker)|A piece of infrastructure that different services use to intercommunicate, via events.|

# Appendix B. A Template Project Structure

Around [Chapter 4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#chapter_04_service_layer), we moved from just having everything in one folder to a more structured tree, and we thought it might be of interest to outline the moving parts.

###### TIP

The code for this appendix is in the appendix_project_structure branch [on GitHub](https://oreil.ly/1rDRC):

git clone https://github.com/cosmicpython/code.git
cd code
git checkout appendix_project_structure

The basic folder structure looks like this:

_Project tree_

```
.
├── Dockerfile  
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO1-1)

Our _docker-compose.yml_ and our _Dockerfile_ are the main bits of configuration for the containers that run our app, and they can also run the tests (for CI). A more complex project might have several Dockerfiles, although we’ve found that minimizing the number of images is usually a good idea.[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288831448)

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO1-2)

A _Makefile_ provides the entrypoint for all the typical commands a developer (or a CI server) might want to run during their normal workflow: `make build`, `make test`, and so on.[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288826472) This is optional. You could just use `docker-compose` and `pytest` directly, but if nothing else, it’s nice to have all the “common commands” in a list somewhere, and unlike documentation, a Makefile is code so it has less tendency to become out of date.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO1-4)

All the source code for our app, including the domain model, the Flask app, and infrastructure code, lives in a Python package inside _src_,[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288791320) which we install using `pip install -e` and the _setup.py_ file. This makes imports easy. Currently, the structure within this module is totally flat, but for a more complex project, you’d expect to grow a folder hierarchy that includes _domain_model/_, _infrastructure/_, _services/_, and _api/_.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO1-6)

Tests live in their own folder. Subfolders distinguish different test types and allow you to run them separately. We can keep shared fixtures (_conftest.py_) in the main tests folder and nest more specific ones if we wish. This is also the place to keep _pytest.ini_.

###### TIP

The [pytest docs](https://oreil.ly/QVb9Q) are really good on test layout and importability.

Let’s look at a few of these files and concepts in more detail.

# Env Vars, 12-Factor, and Config, Inside and Outside Containers

The basic problem we’re trying to solve here is that we need different config settings for the following:

- Running code or tests directly from your own dev machine, perhaps talking to mapped ports from Docker containers
    
- Running on the containers themselves, with “real” ports and hostnames
    
- Different container environments (dev, staging, prod, and so on)
    

Configuration through environment variables as suggested by the [12-factor manifesto](https://12factor.net/config) will solve this problem, but concretely, how do we implement it in our code and our containers?

# Config.py

Whenever our application code needs access to some config, it’s going to get it from a file called _config.py_. Here are a couple of examples from our app:

_Sample config functions (src/allocation/config.py)_

```
import
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO2-1)

We use functions for getting the current config, rather than constants available at import time, because that allows client code to modify `os.environ` if it needs to.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO2-2)

_config.py_ also defines some default settings, designed to work when running the code from the developer’s local machine.[4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288694680)

An elegant Python package called [_environ-config_](https://github.com/hynek/environ-config) is worth looking at if you get tired of hand-rolling your own environment-based config functions.

###### TIP

Don’t let this config module become a dumping ground that is full of things only vaguely related to config and that is then imported all over the place. Keep things immutable and modify them only via environment variables. If you decide to use a [bootstrap script](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#chapter_13_dependency_injection), you can make it the only place (other than tests) that config is imported to.

# Docker-Compose and Containers Config

We use a lightweight Docker container orchestration tool called _docker-compose_. It’s main configuration is via a YAML file (sigh):[5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288688360)

_docker-compose config file (docker-compose.yml)_

```
version
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-1)

In the _docker-compose_ file, we define the different _services_ (containers) that we need for our app. Usually one main image contains all our code, and we can use it to run our API, our tests, or any other service that needs access to the domain model.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-7)

You’ll probably have other infrastructure services, including a database. In production you might not use containers for this; you might have a cloud provider instead, but _docker-compose_ gives us a way of producing a similar service for dev or CI.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-2)

The `environment` stanza lets you set the environment variables for your containers, the hostnames and ports as seen from inside the Docker cluster. If you have enough containers that information starts to be duplicated in these sections, you can use `environment_file` instead. We usually call ours _container.env_.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-3)

Inside a cluster, _docker-compose_ sets up networking such that containers are available to each other via hostnames named after their service name.

[![5](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/5.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-4)

Pro tip: if you’re mounting volumes to share source folders between your local dev machine and the container, the `PYTHONDONTWRITEBYTECODE` environment variable tells Python to not write _.pyc_ files, and that will save you from having millions of root-owned files sprinkled all over your local filesystem, being all annoying to delete and causing weird Python compiler errors besides.

[![6](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/6.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-5)

Mounting our source and test code as `volumes` means we don’t need to rebuild our containers every time we make a code change.

[![7](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/7.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO3-6)

The `ports` section allows us to expose the ports from inside the containers to the outside world[6](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288417784)—these correspond to the default ports we set in _config.py_.

###### NOTE

Inside Docker, other containers are available through hostnames named after their service name. Outside Docker, they are available on `localhost`, at the port defined in the `ports` section.

# Installing Your Source as a Package

All our application code (everything except tests, really) lives inside an _src_ folder:

_The src folder_

```
├── src
│   ├── allocation  
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO4-1)

Subfolders define top-level module names. You can have multiple if you like.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO4-2)

And _setup.py_ is the file you need to make it pip-installable, shown next.

_pip-installable modules in three lines (src/setup.py)_

```
from
```

That’s all you need. `packages=` specifies the names of subfolders that you want to install as top-level modules. The `name` entry is just cosmetic, but it’s required. For a package that’s never actually going to hit PyPI, it’ll do fine.[7](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288355832)

# Dockerfile

Dockerfiles are going to be very project-specific, but here are a few key stages you’ll expect to see:

_Our Dockerfile (Dockerfile)_

```
FROM
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO5-1)

Installing system-level dependencies

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO5-2)

Installing our Python dependencies (you may want to split out your dev from prod dependencies; we haven’t here, for simplicity)

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO5-3)

Copying and installing our source

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#co_a_template_project_structure_CO5-4)

Optionally configuring a default startup command (you’ll probably override this a lot from the command line)

###### TIP

One thing to note is that we install things in the order of how frequently they are likely to change. This allows us to maximize Docker build cache reuse. I can’t tell you how much pain and frustration underlies this lesson. For this and many more Python Dockerfile improvement tips, check out [“Production-Ready Docker Packaging”](https://pythonspeed.com/docker).

# Tests

Our tests are kept alongside everything else, as shown here:

_Tests folder tree_

└── tests
    ├── conftest.py
    ├── e2e
    │   └── test_api.py
    ├── integration
    │   ├── test_orm.py
    │   └── test_repository.py
    ├── pytest.ini
    └── unit
        ├── test_allocate.py
        ├── test_batches.py
        └── test_services.py

Nothing particularly clever here, just some separation of different test types that you’re likely to want to run separately, and some files for common fixtures, config, and so on.

There’s no _src_ folder or _setup.py_ in the test folders because we usually haven’t needed to make tests pip-installable, but if you have difficulties with import paths, you might find it helps.

# Wrap-Up

These are our basic building blocks:

- Source code in an _src_ folder, pip-installable using _setup.py_
    
- Some Docker config for spinning up a local cluster that mirrors production as far as possible
    
- Configuration via environment variables, centralized in a Python file called _config.py_, with defaults allowing things to run _outside_ containers
    
- A Makefile for useful command-line, um, commands
    

We doubt that anyone will end up with _exactly_ the same solutions we did, but we hope you find some inspiration here.

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288831448-marker) Splitting out images for production and testing is sometimes a good idea, but we’ve tended to find that going further and trying to split out different images for different types of application code (e.g., Web API versus pub/sub client) usually ends up being more trouble than it’s worth; the cost in terms of complexity and longer rebuild/CI times is too high. YMMV.

[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288826472-marker) A pure-Python alternative to Makefiles is [Invoke](http://www.pyinvoke.org/), worth checking out if everyone on your team knows Python (or at least knows it better than Bash!).

[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288791320-marker) [“Testing and Packaging”](https://hynek.me/articles/testing-packaging) by Hynek Schlawack provides more information on _src_ folders.

[4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288694680-marker) This gives us a local development setup that “just works” (as much as possible). You may prefer to fail hard on missing environment variables instead, particularly if any of the defaults would be insecure in production.

[5](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288688360-marker) Harry is a bit YAML-weary. It’s _everywhere_, and yet he can never remember the syntax or how it’s supposed to indent.

[6](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288417784-marker) On a CI server, you may not be able to expose arbitrary ports reliably, but it’s only a convenience for local dev. You can find ways of making these port mappings optional (e.g., with _docker-compose.override.yml_).

[7](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app02.html#idm45846288355832-marker) For more _setup.py_ tips, see [this article on packaging](https://oreil.ly/KMWDz) by Hynek.

# Appendix C. Swapping Out the Infrastructure: Do Everything with CSVs

This appendix is intended as a little illustration of the benefits of the Repository, Unit of Work, and Service Layer patterns. It’s intended to follow from [Chapter 6](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch06.html#chapter_06_uow).

Just as we finish building out our Flask API and getting it ready for release, the business comes to us apologetically, saying they’re not ready to use our API and asking if we could build a thing that reads just batches and orders from a couple of CSVs and outputs a third CSV with allocations.

Ordinarily this is the kind of thing that might have a team cursing and spitting and making notes for their memoirs. But not us! Oh no, we’ve ensured that our infrastructure concerns are nicely decoupled from our domain model and service layer. Switching to CSVs will be a simple matter of writing a couple of new `Repository` and `UnitOfWork` classes, and then we’ll be able to reuse _all_ of our logic from the domain layer and the service layer.

Here’s an E2E test to show you how the CSVs flow in and out:

_A first CSV test (tests/e2e/test_csv.py)_

```
def
```

Diving in and implementing without thinking about repositories and all that jazz, you might start with something like this:

_A first cut of our CSV reader/writer (src/bin/allocate-from-csv)_

```
#!/usr/bin/env python
```

It’s not looking too bad! And we’re reusing our domain model objects and our domain service.

But it’s not going to work. Existing allocations need to also be part of our permanent CSV storage. We can write a second test to force us to improve things:

_And another one, with existing allocations (tests/e2e/test_csv.py)_

```
def
```

And we could keep hacking about and adding extra lines to that `load_batches` function, and some sort of way of tracking and saving new allocations—but we already have a model for doing that! It’s called our Repository and Unit of Work patterns.

All we need to do (“all we need to do”) is reimplement those same abstractions, but with CSVs underlying them instead of a database. And as you’ll see, it really is relatively straightforward.

# Implementing a Repository and Unit of Work for CSVs

Here’s what a CSV-based repository could look like. It abstracts away all the logic for reading CSVs from disk, including the fact that it has to read _two different CSVs_ (one for batches and one for allocations), and it gives us just the familiar `.list()` API, which provides the illusion of an in-memory collection of domain objects:

_A repository that uses CSV as its storage mechanism (src/allocation/service_layer/csv_uow.py)_

```
class
```

And here’s what a UoW for CSVs would look like:

_A UoW for CSVs: commit = csv.writer (src/allocation/service_layer/csv_uow.py)_

```
class
```

And once we have that, our CLI app for reading and writing batches and allocations to CSV is pared down to what it should be—a bit of code for reading order lines, and a bit of code that invokes our _existing_ service layer:

_Allocation with CSVs in nine lines (src/bin/allocate-from-csv)_

```
def
```

Ta-da! _Now are y’all impressed or what_?

Much love,

Bob and Harry

# Appendix D. Repository and Unit of Work Patterns with Django

Suppose you wanted to use Django instead of SQLAlchemy and Flask. How might things look? The first thing is to choose where to install it. We put it in a separate package next to our main allocation code:

```
├──
```

###### TIP

The code for this appendix is in the appendix_django branch [on GitHub](https://oreil.ly/A-I76):

git clone https://github.com/cosmicpython/code.git
cd code
git checkout appendix_django

# Repository Pattern with Django

We used a plug-in called [`pytest-django`](https://github.com/pytest-dev/pytest-django) to help with test database management.

Rewriting the first repository test was a minimal change—just rewriting some raw SQL with a call to the Django ORM/QuerySet language:

_First repository test adapted (tests/integration/test_repository.py)_

```
from
```

The second test is a bit more involved since it has allocations, but it is still made up of familiar-looking Django code:

_Second repository test is more involved (tests/integration/test_repository.py)_

```
@pytest.mark.django_db
```

Here’s how the actual repository ends up looking:

_A Django repository (src/allocation/adapters/repository.py)_

```
class
```

You can see that the implementation relies on the Django models having some custom methods for translating to and from our domain model.[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#idm45846285857752)

## Custom Methods on Django ORM Classes to Translate to/from Our Domain Model

Those custom methods look something like this:

_Django ORM with custom methods for domain model conversion (src/djangoproject/alloc/models.py)_

```
from
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO1-1)

For value objects, `objects.get_or_create` can work, but for entities, you probably need an explicit try-get/except to handle the upsert.[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#idm45846285566424)

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO1-3)

We’ve shown the most complex example here. If you do decide to do this, be aware that there will be boilerplate! Thankfully it’s not very complex boilerplate.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO1-4)

Relationships also need some careful, custom handling.

###### NOTE

As in [Chapter 2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch02.html#chapter_02_repository), we use dependency inversion. The ORM (Django) depends on the model and not the other way around.

# Unit of Work Pattern with Django

The tests don’t change too much:

_Adapted UoW tests (tests/integration/test_uow.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO2-1)

Because we had little helper functions in these tests, the actual main bodies of the tests are pretty much the same as they were with SQLAlchemy.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO2-3)

The `pytest-django` `mark.django_db(transaction=True)` is required to test our custom transaction/rollback behaviors.

And the implementation is quite simple, although it took me a few tries to find which invocation of Django’s transaction magic would work:

_UoW adapted for Django (src/allocation/service_layer/unit_of_work.py)_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO3-1)

`set_autocommit(False)` was the best way to tell Django to stop automatically committing each ORM operation immediately, and to begin a transaction.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO3-4)

Then we use the explicit rollback and commits.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#co_repository_and_unit_of_work__span_class__keep_together__patterns_with_django__span__CO3-2)

One difficulty: because, unlike with SQLAlchemy, we’re not instrumenting the domain model instances themselves, the `commit()` command needs to explicitly go through all the objects that have been touched by every repository and manually update them back to the ORM.

# API: Django Views Are Adapters

The Django _views.py_ file ends up being almost identical to the old _flask_app.py_, because our architecture means it’s a very thin wrapper around our service layer (which didn’t change at all, by the way):

_Flask app → Django views (src/djangoproject/alloc/views.py)_

```
os
```

# Why Was This All So Hard?

OK, it works, but it does feel like more effort than Flask/SQLAlchemy. Why is that?

The main reason at a low level is because Django’s ORM doesn’t work in the same way. We don’t have an equivalent of the SQLAlchemy classical mapper, so our `ActiveRecord` and our domain model can’t be the same object. Instead we have to build a manual translation layer behind the repository. That’s more work (although once it’s done, the ongoing maintenance burden shouldn’t be too high).

Because Django is so tightly coupled to the database, you have to use helpers like `pytest-django` and think carefully about test databases, right from the very first line of code, in a way that we didn’t have to when we started out with our pure domain model.

But at a higher level, the entire reason that Django is so great is that it’s designed around the sweet spot of making it easy to build CRUD apps with minimal boilerplate. But the entire thrust of our book is about what to do when your app is no longer a simple CRUD app.

At that point, Django starts hindering more than it helps. Things like the Django admin, which are so awesome when you start out, become actively dangerous if the whole point of your app is to build a complex set of rules and modeling around the workflow of state changes. The Django admin bypasses all of that.

# What to Do If You Already Have Django

So what should you do if you want to apply some of the patterns in this book to a Django app? We’d say the following:

- The Repository and Unit of Work patterns are going to be quite a lot of work. The main thing they will buy you in the short term is faster unit tests, so evaluate whether that benefit feels worth it in your case. In the longer term, they decouple your app from Django and the database, so if you anticipate wanting to migrate away from either of those, Repository and UoW are a good idea.
    
- The Service Layer pattern might be of interest if you’re seeing a lot of duplication in your _views.py_. It can be a good way of thinking about your use cases separately from your web endpoints.
    
- You can still theoretically do DDD and domain modeling with Django models, tightly coupled as they are to the database; you may be slowed by migrations, but it shouldn’t be fatal. So as long as your app is not too complex and your tests not too slow, you may be able to get something out of the _fat models_ approach: push as much logic down to your models as possible, and apply patterns like Entity, Value Object, and Aggregate. However, see the following caveat.
    

With that said, [word in the Django community](https://oreil.ly/Nbpjj) is that people find that the fat models approach runs into scalability problems of its own, particularly around managing interdependencies between apps. In those cases, there’s a lot to be said for extracting out a business logic or domain layer to sit between your views and forms and your _models.py_, which you can then keep as minimal as possible.

# Steps Along the Way

Suppose you’re working on a Django project that you’re not sure is going to get complex enough to warrant the patterns we recommend, but you still want to put a few steps in place to make your life easier, both in the medium term and if you want to migrate to some of our patterns later. Consider the following:

- One piece of advice we’ve heard is to put a _logic.py_ into every Django app from day one. This gives you a place to put business logic, and to keep your forms, views, and models free of business logic. It can become a stepping-stone for moving to a fully decoupled domain model and/or service layer later.
    
- A business-logic layer might start out working with Django model objects and only later become fully decoupled from the framework and work on plain Python data structures.
    

- For the read side, you can get some of the benefits of CQRS by putting reads into one place, avoiding ORM calls sprinkled all over the place.
    
- When separating out modules for reads and modules for domain logic, it may be worth decoupling yourself from the Django apps hierarchy. Business concerns will cut across them.
    

###### NOTE

We’d like to give a shout-out to David Seddon and Ashia Zawaduk for talking through some of the ideas in this appendix. They did their best to stop us from saying anything really stupid about a topic we don’t really have enough personal experience of, but they may have failed.

For more thoughts and actual lived experience dealing with existing applications, refer to the [epilogue](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/afterword01.html#epilogue_1_how_to_get_there_from_here).

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#idm45846285857752-marker) The DRY-Python project people have built a tool called [mappers](https://mappers.readthedocs.io/en/latest) that looks like it might help minimize boilerplate for this sort of thing.

[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app04.html#idm45846285566424-marker) `@mr-bo-jangles` suggested you might be able to use [`update_or_create`](https://oreil.ly/HTq1r), but that’s beyond our Django-fu.

# Appendix E. Validation

Whenever we’re teaching and talking about these techniques, one question that comes up over and over is “Where should I do validation? Does that belong with my business logic in the domain model, or is that an infrastructural concern?”

As with any architectural question, the answer is: it depends!

The most important consideration is that we want to keep our code well separated so that each part of the system is simple. We don’t want to clutter our code with irrelevant detail.

# What Is Validation, Anyway?

When people use the word _validation_, they usually mean a process whereby they test the inputs of an operation to make sure that they match certain criteria. Inputs that match the criteria are considered _valid_, and inputs that don’t are _invalid_.

If the input is invalid, the operation can’t continue but should exit with some kind of error. In other words, validation is about creating _preconditions_. We find it useful to separate our preconditions into three subtypes: syntax, semantics, and pragmatics.

# Validating Syntax

In linguistics, the _syntax_ of a language is the set of rules that govern the structure of grammatical sentences. For example, in English, the sentence “Allocate three units of `TASTELESS-LAMP` to order twenty-seven” is grammatically sound, while the phrase “hat hat hat hat hat hat wibble” is not. We can describe grammatically correct sentences as _well formed_.

How does this map to our application? Here are some examples of syntactic rules:

- An `Allocate` command must have an order ID, a SKU, and a quantity.
    
- A quantity is a positive integer.
    
- A SKU is a string.
    

These are rules about the shape and structure of incoming data. An `Allocate` command without a SKU or an order ID isn’t a valid message. It’s the equivalent of the phrase “Allocate three to.”

We tend to validate these rules at the edge of the system. Our rule of thumb is that a message handler should always receive only a message that is well-formed and contains all required information.

One option is to put your validation logic on the message type itself:

_Validation on the message class (src/allocation/commands.py)_

```
from
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO1-1)

The [`schema` library](https://pypi.org/project/schema) lets us describe the structure and validation of our messages in a nice declarative way.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO1-2)

The `from_json` method reads a string as JSON and turns it into our message type.

This can get repetitive, though, since we need to specify our fields twice, so we might want to introduce a helper library that can unify the validation and declaration of our message types:

_A command factory with schema (src/allocation/commands.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO2-1)

The `command` function takes a message name, plus kwargs for the fields of the message payload, where the name of the kwarg is the name of the field and the value is the parser.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO2-2)

We use the `make_dataclass` function from the dataclass module to dynamically create our message type.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO2-3)

We patch the `from_json` method onto our dynamic dataclass.

[![4](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/4.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO2-4)

We can create reusable parsers for quantity, SKU, and so on to keep things DRY.

[![5](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/5.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO2-5)

Declaring a message type becomes a one-liner.

This comes at the expense of losing the types on your dataclass, so bear that trade-off in mind.

# Postel’s Law and the Tolerant Reader Pattern

_Postel’s law_, or the _robustness principle_, tells us, “Be liberal in what you accept, and conservative in what you emit.” We think this applies particularly well in the context of integration with our other systems. The idea here is that we should be strict whenever we’re sending messages to other systems, but as lenient as possible when we’re receiving messages from others.

For example, our system _could_ validate the format of a SKU. We’ve been using made-up SKUs like `UNFORGIVING-CUSHION` and `MISBEGOTTEN-POUFFE`. These follow a simple pattern: two words, separated by dashes, where the second word is the type of product and the first word is an adjective.

Developers _love_ to validate this kind of thing in their messages, and reject anything that looks like an invalid SKU. This causes horrible problems down the line when some anarchist releases a product named `COMFY-CHAISE-LONGUE` or when a snafu at the supplier results in a shipment of `CHEAP-CARPET-2`.

Really, as the allocation system, it’s _none of our business_ what the format of a SKU might be. All we need is an identifier, so we can simply describe it as a string. This means that the procurement system can change the format whenever they like, and we won’t care.

This same principle applies to order numbers, customer phone numbers, and much more. For the most part, we can ignore the internal structure of strings.

Similarly, developers _love_ to validate incoming messages with tools like JSON Schema, or to build libraries that validate incoming messages and share them among systems. This likewise fails the robustness test.

Let’s imagine, for example, that the procurement system adds new fields to the `ChangeBatchQuantity` message that record the reason for the change and the email of the user responsible for the change.

Since these fields don’t matter to the allocation service, we should simply ignore them. We can do that in the `schema` library by passing the keyword arg `ignore_extra_keys=True`.

This pattern, whereby we extract only the fields we care about and do minimal validation of them, is the Tolerant Reader pattern.

###### TIP

Validate as little as possible. Read only the fields you need, and don’t overspecify their contents. This will help your system stay robust when other systems change over time. Resist the temptation to share message definitions between systems: instead, make it easy to define the data you depend on. For more info, see Martin Fowler’s article on the [Tolerant Reader pattern](https://oreil.ly/YL_La).

##### IS POSTEL ALWAYS RIGHT?

Mentioning Postel can be quite triggering to some people. They will [tell you](https://oreil.ly/bzLmb) that Postel is the precise reason that everything on the internet is broken and we can’t have nice things. Ask Hynek about SSLv3 one day.

We like the Tolerant Reader approach in the particular context of event-based integration between services that we control, because it allows for independent evolution of those services.

If you’re in charge of an API that’s open to the public on the big bad internet, there might be good reasons to be more conservative about what inputs you allow.

# Validating at the Edge

Earlier, we said that we want to avoid cluttering our code with irrelevant details. In particular, we don’t want to code defensively inside our domain model. Instead, we want to make sure that requests are known to be valid before our domain model or use-case handlers see them. This helps our code stay clean and maintainable over the long term. We sometimes refer to this as _validating at the edge of the system_.

In addition to keeping your code clean and free of endless checks and asserts, bear in mind that invalid data wandering through your system is a time bomb; the deeper it gets, the more damage it can do, and the fewer tools you have to respond to it.

Back in [Chapter 8](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch08.html#chapter_08_events_and_message_bus), we said that the message bus was a great place to put cross-cutting concerns, and validation is a perfect example of that. Here’s how we might change our bus to perform validation for us:

_Validation_

```
class
```

Here’s how we might use that method from our Flask API endpoint:

_API bubbles up validation errors (src/allocation/flask_app.py)_

```
@app.route
```

And here’s how we might plug it in to our asynchronous message processor:

_Validation errors when handling Redis messages (src/allocation/redis_pubsub.py)_

```
def
```

Notice that our entrypoints are solely concerned with how to get a message from the outside world and how to report success or failure. Our message bus takes care of validating our requests and routing them to the correct handler, and our handlers are exclusively focused on the logic of our use case.

###### TIP

When you receive an invalid message, there’s usually little you can do but log the error and continue. At MADE we use metrics to count the number of messages a system receives, and how many of those are successfully processed, skipped, or invalid. Our monitoring tools will alert us if we see spikes in the numbers of bad messages.

# Validating Semantics

While syntax is concerned with the structure of messages, _semantics_ is the study of _meaning_ in messages. The sentence “Undo no dogs from ellipsis four” is syntactically valid and has the same structure as the sentence “Allocate one teapot to order five,"” but it is meaningless.

We can read this JSON blob as an `Allocate` command but can’t successfully execute it, because it’s _nonsense_:

_A meaningless message_

```
{
```

We tend to validate semantic concerns at the message-handler layer with a kind of contract-based programming:

_Preconditions (src/allocation/ensure.py)_

```
"""
This module contains preconditions that we apply to our handlers.
"""
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO3-1)

We use a common base class for errors that mean a message is invalid.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO3-2)

Using a specific error type for this problem makes it easier to report on and handle the error. For example, it’s easy to map `ProductNotFound` to a 404 in Flask.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/app05.html#co_validation_CO3-3)

`product_exists` is a precondition. If the condition is `False`, we raise an error.

This keeps the main flow of our logic in the service layer clean and declarative:

_Ensure calls in services (src/allocation/services.py)_

```
# services.py
```

We can extend this technique to make sure that we apply messages idempotently. For example, we want to make sure that we don’t insert a batch of stock more than once.

If we get asked to create a batch that already exists, we’ll log a warning and continue to the next message:

_Raise SkipMessage exception for ignorable events (src/allocation/services.py)_

```
class
```

Introducing a `SkipMessage` exception lets us handle these cases in a generic way in our message bus:

_The bus now knows how to skip (src/allocation/messagebus.py)_

```
class
```

There are a couple of pitfalls to be aware of here. First, we need to be sure that we’re using the same UoW that we use for the main logic of our use case. Otherwise, we open ourselves to irritating concurrency bugs.

Second, we should try to avoid putting _all_ our business logic into these precondition checks. As a rule of thumb, if a rule _can_ be tested inside our domain model, then it _should_ be tested in the domain model.

# Validating Pragmatics

_Pragmatics_ is the study of how we understand language in context. After we have parsed a message and grasped its meaning, we still need to process it in context. For example, if you get a comment on a pull request saying, “I think this is very brave,” it may mean that the reviewer admires your courage—unless they’re British, in which case, they’re trying to tell you that what you’re doing is insanely risky, and only a fool would attempt it. Context is everything.

##### VALIDATION RECAP

Validation means different things to different people

When talking about validation, make sure you’re clear about what you’re validating. We find it useful to think about syntax, semantics, and pragmatics: the structure of messages, the meaningfulness of messages, and the business logic governing our response to messages.

Validate at the edge when possible

Validating required fields and the permissible ranges of numbers is _boring_, and we want to keep it out of our nice clean codebase. Handlers should always receive only valid messages.

Only validate what you require

Use the Tolerant Reader pattern: read only the fields your application needs and don’t overspecify their internal structure. Treating fields as opaque strings buys you a lot of flexibility.

Spend time writing helpers for validation

Having a nice declarative way to validate incoming messages and apply preconditions to your handlers will make your codebase much cleaner. It’s worth investing time to make boring code easy to maintain.

Locate each of the three types of validation in the right place

Validating syntax can happen on message classes, validating semantics can happen in the service layer or on the message bus, and validating pragmatics belongs in the domain model.

###### TIP

Once you’ve validated the syntax and semantics of your commands at the edges of your system, the domain is the place for the rest of your validation. Validation of pragmatics is often a core part of your business rules.

In software terms, the pragmatics of an operation are usually managed by the domain model. When we receive a message like “allocate three million units of `SCARCE-CLOCK` to order 76543,” the message is _syntactically_ valid and _semantically_ valid, but we’re unable to comply because we don’t have the stock available.
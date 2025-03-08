# Chapter 3. A Brief Interlude: On Coupling and Abstractions

Allow us a brief digression on the subject of abstractions, dear reader. We’ve talked about _abstractions_ quite a lot. The Repository pattern is an abstraction over permanent storage, for example. But what makes a good abstraction? What do we want from abstractions? And how do they relate to testing?

###### TIP

The code for this chapter is in the chapter_03_abstractions branch [on GitHub](https://oreil.ly/k6MmV):

git clone https://github.com/cosmicpython/code.git
git checkout chapter_03_abstractions

A key theme in this book, hidden among the fancy patterns, is that we can use simple abstractions to hide messy details. When we’re writing code for fun, or in a kata,[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#idm45846314230072) we get to play with ideas freely, hammering things out and refactoring aggressively. In a large-scale system, though, we become constrained by the decisions made elsewhere in the system.

When we’re unable to change component A for fear of breaking component B, we say that the components have become _coupled_. Locally, coupling is a good thing: it’s a sign that our code is working together, each component supporting the others, all of them fitting in place like the gears of a watch. In jargon, we say this works when there is high _cohesion_ between the coupled elements.

Globally, coupling is a nuisance: it increases the risk and the cost of changing our code, sometimes to the point where we feel unable to make any changes at all. This is the problem with the Ball of Mud pattern: as the application grows, if we’re unable to prevent coupling between elements that have no cohesion, that coupling increases superlinearly until we are no longer able to effectively change our systems.

We can reduce the degree of coupling within a system ([Figure 3-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#coupling_illustration1)) by abstracting away the details ([Figure 3-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#coupling_illustration2)).

![apwp 0301](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0301.png)

###### Figure 3-1. Lots of coupling

![apwp 0302](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/apwp_0302.png)

###### Figure 3-2. Less coupling

In both diagrams, we have a pair of subsystems, with one dependent on the other. In [Figure 3-1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#coupling_illustration1), there is a high degree of coupling between the two; the number of arrows indicates lots of kinds of dependencies between the two. If we need to change system B, there’s a good chance that the change will ripple through to system A.

In [Figure 3-2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#coupling_illustration2), though, we have reduced the degree of coupling by inserting a new, simpler abstraction. Because it is simpler, system A has fewer kinds of dependencies on the abstraction. The abstraction serves to protect us from change by hiding away the complex details of whatever system B does—we can change the arrows on the right without changing the ones on the left.

# Abstracting State Aids Testability

Let’s see an example. Imagine we want to write code for synchronizing two file directories, which we’ll call the _source_ and the _destination_:

- If a file exists in the source but not in the destination, copy the file over.
    
- If a file exists in the source, but it has a different name than in the destination, rename the destination file to match.
    
- If a file exists in the destination but not in the source, remove it.
    

Our first and third requirements are simple enough: we can just compare two lists of paths. Our second is trickier, though. To detect renames, we’ll have to inspect the content of files. For this, we can use a hashing function like MD5 or SHA-1. The code to generate a SHA-1 hash from a file is simple enough:

_Hashing a file (sync.py)_

```
BLOCKSIZE
```

Now we need to write the bit that makes decisions about what to do—the business logic, if you will.

When we have to tackle a problem from first principles, we usually try to write a simple implementation and then refactor toward better design. We’ll use this approach throughout the book, because it’s how we write code in the real world: start with a solution to the smallest part of the problem, and then iteratively make the solution richer and better designed.

Our first hackish approach looks something like this:

_Basic sync algorithm (sync.py)_

```
import
```

Fantastic! We have some code and it _looks_ OK, but before we run it on our hard drive, maybe we should test it. How do we go about testing this sort of thing?

_Some end-to-end tests (test_sync.py)_

```
def
```

Wowsers, that’s a lot of setup for two simple cases! The problem is that our domain logic, “figure out the difference between two directories,” is tightly coupled to the I/O code. We can’t run our difference algorithm without calling the `pathlib`, `shutil`, and `hashlib` modules.

And the trouble is, even with our current requirements, we haven’t written enough tests: the current implementation has several bugs (the `shutil.move()` is wrong, for example). Getting decent coverage and revealing these bugs means writing more tests, but if they’re all as unwieldy as the preceding ones, that’s going to get real painful real quickly.

On top of that, our code isn’t very extensible. Imagine trying to implement a `--dry-run` flag that gets our code to just print out what it’s going to do, rather than actually do it. Or what if we wanted to sync to a remote server, or to cloud storage?

Our high-level code is coupled to low-level details, and it’s making life hard. As the scenarios we consider get more complex, our tests will get more unwieldy. We can definitely refactor these tests (some of the cleanup could go into pytest fixtures, for example) but as long as we’re doing filesystem operations, they’re going to stay slow and be hard to read and write.

# Choosing the Right Abstraction(s)

What could we do to rewrite our code to make it more testable?

First, we need to think about what our code needs from the filesystem. Reading through the code, we can see that three distinct things are happening. We can think of these as three distinct _responsibilities_ that the code has:

1. We interrogate the filesystem by using `os.walk` and determine hashes for a series of paths. This is similar in both the source and the destination cases.
    
2. We decide whether a file is new, renamed, or redundant.
    
3. We copy, move, or delete files to match the source.
    

Remember that we want to find _simplifying abstractions_ for each of these responsibilities. That will let us hide the messy details so we can focus on the interesting logic.[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#idm45846313635768)

###### NOTE

In this chapter, we’re refactoring some gnarly code into a more testable structure by identifying the separate tasks that need to be done and giving each task to a clearly defined actor, along similar lines to [the `duckduckgo` example](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/preface02.html#ddg_example).

For steps 1 and 2, we’ve already intuitively started using an abstraction, a dictionary of hashes to paths. You may already have been thinking, “Why not build up a dictionary for the destination folder as well as the source, and then we just compare two dicts?” That seems like a nice way to abstract the current state of the filesystem:

source_files = {'hash1': 'path1', 'hash2': 'path2'}
dest_files = {'hash1': 'path1', 'hash2': 'pathX'}

What about moving from step 2 to step 3? How can we abstract out the actual move/copy/delete filesystem interaction?

We’ll apply a trick here that we’ll employ on a grand scale later in the book. We’re going to separate _what_ we want to do from _how_ to do it. We’re going to make our program output a list of commands that look like this:

("COPY", "sourcepath", "destpath"),
("MOVE", "old", "new"),

Now we could write tests that just use two filesystem dicts as inputs, and we would expect lists of tuples of strings representing actions as outputs.

Instead of saying, “Given this actual filesystem, when I run my function, check what actions have happened,” we say, “Given this _abstraction_ of a filesystem, what _abstraction_ of filesystem actions will happen?”

_Simplified inputs and outputs in our tests (test_sync.py)_

    `def` `test_when_a_file_exists_in_the_source_but_not_the_destination``():`
        `src_hashes` `=` `{``'hash1'``:` `'fn1'``}`
        `dst_hashes` `=` `{}`
        `expected_actions` `=` `[(``'COPY'``,` `'/src/fn1'``,` `'/dst/fn1'``)]`
        `...`

    `def` `test_when_a_file_has_been_renamed_in_the_source``():`
        `src_hashes` `=` `{``'hash1'``:` `'fn1'``}`
        `dst_hashes` `=` `{``'hash1'``:` `'fn2'``}`
        `expected_actions` `==` `[(``'MOVE'``,` `'/dst/fn2'``,` `'/dst/fn1'``)]`
        `...`

# Implementing Our Chosen Abstractions

That’s all very well, but how do we _actually_ write those new tests, and how do we change our implementation to make it all work?

Our goal is to isolate the clever part of our system, and to be able to test it thoroughly without needing to set up a real filesystem. We’ll create a “core” of code that has no dependencies on external state and then see how it responds when we give it input from the outside world (this kind of approach was characterized by Gary Bernhardt as [Functional Core, Imperative Shell](https://oreil.ly/wnad4), or FCIS).

Let’s start off by splitting the code to separate the stateful parts from the logic.

And our top-level function will contain almost no logic at all; it’s just an imperative series of steps: gather inputs, call our logic, apply outputs:

_Split our code into three (sync.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO1-1)

Here’s the first function we factor out, `read_paths_and_hashes()`, which isolates the I/O part of our application.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO1-3)

Here is where carve out the functional core, the business logic.

The code to build up the dictionary of paths and hashes is now trivially easy to write:

_A function that just does I/O (sync.py)_

```
def
```

The `determine_actions()` function will be the core of our business logic, which says, “Given these two sets of hashes and filenames, what should we copy/move/delete?”. It takes simple data structures and returns simple data structures:

_A function that just does business logic (sync.py)_

```
def
```

Our tests now act directly on the `determine_actions()` function:

_Nicer-looking tests (test_sync.py)_

```
def
```

Because we’ve disentangled the logic of our program—the code for identifying changes—from the low-level details of I/O, we can easily test the core of our code.

With this approach, we’ve switched from testing our main entrypoint function, `sync()`, to testing a lower-level function, `determine_actions()`. You might decide that’s fine because `sync()` is now so simple. Or you might decide to keep some integration/acceptance tests to test that `sync()`. But there’s another option, which is to modify the `sync()` function so it can be unit tested _and_ end-to-end tested; it’s an approach Bob calls _edge-to-edge testing_.

## Testing Edge to Edge with Fakes and Dependency Injection

When we start writing a new system, we often focus on the core logic first, driving it with direct unit tests. At some point, though, we want to test bigger chunks of the system together.

We _could_ return to our end-to-end tests, but those are still as tricky to write and maintain as before. Instead, we often write tests that invoke a whole system together but fake the I/O, sort of _edge to edge_:

_Explicit dependencies (sync.py)_

```
def
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO2-1)

Our top-level function now exposes two new dependencies, a `reader` and a `filesystem`.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO2-2)

We invoke the `reader` to produce our files dict.

[![3](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/3.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO2-3)

We invoke the `filesystem` to apply the changes we detect.

###### TIP

Although we’re using dependency injection, there is no need to define an abstract base class or any kind of explicit interface. In this book, we often show ABCs because we hope they help you understand what the abstraction is, but they’re not necessary. Python’s dynamic nature means we can always rely on duck typing.

_Tests using DI_

```
class
```

[![1](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/1.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO3-1)

Bob _loves_ using lists to build simple test doubles, even though his coworkers get mad. It means we can write tests like `assert _foo_ not in database`.

[![2](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052197/files/assets/2.png)](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#co_a_brief_interlude__on_coupling__span_class__keep_together__and_abstractions__span__CO3-2)

Each method in our `FakeFileSystem` just appends something to the list so we can inspect it later. This is an example of a spy object.

The advantage of this approach is that our tests act on the exact same function that’s used by our production code. The disadvantage is that we have to make our stateful components explicit and pass them around. David Heinemeier Hansson, the creator of Ruby on Rails, famously described this as “test-induced design damage.”

In either case, we can now work on fixing all the bugs in our implementation; enumerating tests for all the edge cases is now much easier.

## Why Not Just Patch It Out?

At this point you may be scratching your head and thinking, “Why don’t you just use `mock.patch` and save yourself the effort?"”

We avoid using mocks in this book and in our production code too. We’re not going to enter into a Holy War, but our instinct is that mocking frameworks, particularly monkeypatching, are a code smell.

Instead, we like to clearly identify the responsibilities in our codebase, and to separate those responsibilities into small, focused objects that are easy to replace with a test double.

###### NOTE

You can see an example in [Chapter 8](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch08.html#chapter_08_events_and_message_bus), where we `mock.patch()` out an email-sending module, but eventually we replace that with an explicit bit of dependency injection in [Chapter 13](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#chapter_13_dependency_injection).

We have three closely related reasons for our preference:

- Patching out the dependency you’re using makes it possible to unit test the code, but it does nothing to improve the design. Using `mock.patch` won’t let your code work with a `--dry-run` flag, nor will it help you run against an FTP server. For that, you’ll need to introduce abstractions.
    
- Tests that use mocks _tend_ to be more coupled to the implementation details of the codebase. That’s because mock tests verify the interactions between things: did we call `shutil.copy` with the right arguments? This coupling between code and test _tends_ to make tests more brittle, in our experience.
    
- Overuse of mocks leads to complicated test suites that fail to explain the code.
    

###### NOTE

Designing for testability really means designing for extensibility. We trade off a little more complexity for a cleaner design that admits novel use cases.

##### MOCKS VERSUS FAKES; CLASSIC-STYLE VERSUS LONDON-SCHOOL TDD

Here’s a short and somewhat simplistic definition of the difference between mocks and fakes:

- Mocks are used to verify _how_ something gets used; they have methods like `assert_called_once_with()`. They’re associated with London-school TDD.
    
- Fakes are working implementations of the thing they’re replacing, but they’re designed for use only in tests. They wouldn’t work “in real life”; our in-memory repository is a good example. But you can use them to make assertions about the end state of a system rather than the behaviors along the way, so they’re associated with classic-style TDD.
    

We’re slightly conflating mocks with spies and fakes with stubs here, and you can read the long, correct answer in Martin Fowler’s classic essay on the subject called [“Mocks Aren’t Stubs”](https://oreil.ly/yYjBN).

It also probably doesn’t help that the `MagicMock` objects provided by `unittest.mock` aren’t, strictly speaking, mocks; they’re spies, if anything. But they’re also often used as stubs or dummies. There, we promise we’re done with the test double terminology nitpicks now.

What about London-school versus classic-style TDD? You can read more about those two in Martin Fowler’s article that we just cited, as well as on the [Software Engineering Stack Exchange site](https://oreil.ly/H2im_), but in this book we’re pretty firmly in the classicist camp. We like to build our tests around state both in setup and in assertions, and we like to work at the highest level of abstraction possible rather than doing checks on the behavior of intermediary collaborators.[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#idm45846312505656)

Read more on this in [“On Deciding What Kind of Tests to Write”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#kinds_of_tests).

We view TDD as a design practice first and a testing practice second. The tests act as a record of our design choices and serve to explain the system to us when we return to the code after a long absence.

Tests that use too many mocks get overwhelmed with setup code that hides the story we care about.

Steve Freeman has a great example of overmocked tests in his talk [“Test-Driven Development”](https://oreil.ly/jAmtr). You should also check out this PyCon talk, [“Mocking and Patching Pitfalls”](https://oreil.ly/s3e05), by our esteemed tech reviewer, Ed Jung, which also addresses mocking and its alternatives. And while we’re recommending talks, don’t miss Brandon Rhodes talking about [“Hoisting Your I/O”](https://oreil.ly/oiXJM), which really nicely covers the issues we’re talking about, using another simple example.

###### TIP

In this chapter, we’ve spent a lot of time replacing end-to-end tests with unit tests. That doesn’t mean we think you should never use E2E tests! In this book we’re showing techniques to get you to a decent test pyramid with as many unit tests as possible, and with the minimum number of E2E tests you need to feel confident. Read on to [“Recap: Rules of Thumb for Different Types of Test”](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch05.html#types_of_test_rules_of_thumb) for more details.

##### SO WHICH DO WE USE IN THIS BOOK? FUNCTIONAL OR OBJECT-ORIENTED COMPOSITION?

Both. Our domain model is entirely free of dependencies and side effects, so that’s our functional core. The service layer that we build around it (in [Chapter 4](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch04.html#chapter_04_service_layer)) allows us to drive the system edge to edge, and we use dependency injection to provide those services with stateful components, so we can still unit test them.

See [Chapter 13](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch13.html#chapter_13_dependency_injection) for more exploration of making our dependency injection more explicit and centralized.

# Wrap-Up

We’ll see this idea come up again and again in the book: we can make our systems easier to test and maintain by simplifying the interface between our business logic and messy I/O. Finding the right abstraction is tricky, but here are a few heuristics and questions to ask yourself:

- Can I choose a familiar Python data structure to represent the state of the messy system and then try to imagine a single function that can return that state?
    
- Where can I draw a line between my systems, where can I carve out a [seam](https://oreil.ly/zNUGG) to stick that abstraction in?
    
- What is a sensible way of dividing things into components with different responsibilities? What implicit concepts can I make explicit?
    
- What are the dependencies, and what is the core business logic?
    

Practice makes less imperfect! And now back to our regular programming…

[1](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#idm45846314230072-marker) A code kata is a small, contained programming challenge often used to practice TDD. See [“Kata—The Only Way to Learn TDD”](https://oreil.ly/vhjju) by Peter Provost.

[2](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#idm45846313635768-marker) If you’re used to thinking in terms of interfaces, that’s what we’re trying to define here.

[3](https://learning.oreilly.com/library/view/architecture-patterns-with/9781492052197/ch03.html#idm45846312505656-marker) Which is not to say that we think the London school people are wrong. Some insanely smart people work that way. It’s just not what we’re used to.
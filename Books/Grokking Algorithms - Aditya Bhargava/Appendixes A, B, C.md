# Appendix A. Performance of AVL trees

This appendix discusses the performance of AVL trees, which were introduced in chapter 8. You will need to read that chapter before reading this.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_A-1.png)

Remember that AVL trees offer O(log _n_) search performance. But there is something misleading going on. Here are two trees. Both offer O(log _n_) search performance, but their heights are different!

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_A-2.png)

(Dashed nodes show the holes in the tree.)

AVL trees allow a difference of one in heights. That’s why, even though both these trees have 15 nodes, the perfectly balanced tree is height 3, but the AVL tree is height 4. The perfectly balanced tree is what we might picture a balanced tree to look like, where each level is completely filled with nodes before a new level is added. But the AVL tree is also considered “balanced,” even though it has holes—gaps where a node could be.

Remember that in a tree, performance is closely related to height. How can these trees offer the same performance if their heights are different? Well, we never discussed what the base in log _n_ is!

The perfectly balanced tree has performance O(log _n_), where the “log” is log base 2, just like binary search. We can see that in the picture. Each new level doubles the nodes plus 1. So a perfectly balanced tree of height 1 has 3 nodes, of height 2 has 7 nodes (32 + 1), of height 3 has 15 nodes (72 + 1), etc. You could also think of it as each layer adds a number of nodes equal to a power of 2.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_A-3.png)

So the perfectly balanced tree has performance O(log _n_), where the “log” is log base 2.

The AVL tree has some gaps. In an AVL tree, each new layer adds _less_ than double the nodes. It turns out that an AVL offers performance O(log _n_), but the “log” is log base _phi_ (aka the golden ratio, aka ~1.618).

This is a small but interesting difference—AVL trees offer performance that is not quite as good as perfectly balanced trees since the base is different. But the performance is still very close, since both are O(log _n_) after all. Just know it’s not exactly the same.
# Appendix B. NP-hard problems

Both the set-covering and traveling salesperson problems have something in common: they are hard to solve. You have to check every possible iteration to find the smallest set cover or the shortest route.

Both of these problems are NP-hard. The terms _NP_, _NP-hard_, and _NP-complete_ can cause a lot of confusion. They certainly confused me. In this appendix, I’ll try to explain what all these terms mean, but I need to explain some other concepts first. Here is a roadmap of the things we will learn and how they depend on each other:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-1.png)

But, first, I need to explain what a _decision problem_ is because all the problems we will look at in the rest of this appendix are decision problems.

## Decision problems

NP-complete problems are always decision problems. A decision problem has a yes-or-no answer. The traveling salesperson problem is not a decision problem. It’s asking you to find the shortest path, which is an optimization problem.

NOTE I know I was talking about _NP-hard_ problems in the introduction, and now I’m talking about _NP-complete_ problems. I’ll explain what the difference is soon.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-2.png)

Find the shortest path!

Here’s a decision version of the traveling salesperson problem.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-3.png)

Is there a path of length 5?

Notice how this question has a yes-or-no answer: is there a path of length 5? I wanted to talk about decision problems up front because all NP-complete problems are decision problems. So _all the problems I discuss in the rest of this appendix will be decision problems._ So when you see “traveling salesperson” mentioned in the rest of this appendix, I mean the _decision_ version of the traveling salesperson problem.

Now let’s start learning what NP-complete actually means! The first step is to learn about the satisfiability (SAT) problem.

## The satisfiability problem

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-4.png)

Jerry, George, Elaine, and Kramer are all ordering pizza.

“Ooh, let’s get pepperoni!” says Elaine.

“Pepperoni is good. Sausage is good. We could get pepperoni or sausage,” says Jerry.

“Get me an olive pizza to maintain my complexion,” says Kramer. “_Lots_ of olives. Or, onions.”

“I can do any pizza but _no_ onions,” says George. “I can’t take any more onions, Jerry!”

“Oh boy. OK, let me figure this out. So what toppings do I need again?” says Jerry.

Can you help him out? Here are everyone’s requirements:

- Pepperoni (Elaine)
    
- Pepperoni or sausage (Jerry)
    
- Olives or onions (Kramer)
    
- No onions (George)
    

See if you can figure out what toppings the pizza should have before moving on.

Did you get it? A pepperoni and olive pizza satisfies all the requirements. This is an example of a SAT problem. In pseudocode, I could write it like this. First, I have four boolean variables:

pepperoni = ?
sausage = ?
olives = ?
onions = ?

Then I write out a boolean formula:

(pepperoni) and (pepperoni or sausage) and (olives or onions) and (not onions)

This formula contains the requirements for each person in the form of boolean logic. The SAT problem asks the question: Can you set these variables to some values so that the statement evaluates to `true`?

The SAT problem is famous because it is the first NP-complete problem, described in 1971 (although I don’t think the authors used Seinfeld as an example). Before this, the concept of an NP-complete problem did not exist. Here is how the SAT problem works. You start with a boolean formula:

if (pepperoni) and (olives or onions):
    print("pizza")

Then you ask, is there some way we can assign our variables so that code prints `pizza`?

This example is pretty easy, so we can solve it ourselves. If `pepperoni` and `onions` are `true`, this code will print `pizza`. So the answer would be _yes_.

Here’s one where the answer would be _no_:

if (olives or onions) and (not olives) and (not onions):
    print("pizza")

There is nothing you can set the variables to so this code will print `pizza`!

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-5.png)

The SAT problem always looks for a yes-or-no answer, so it is a _decision_ problem.

SAT is actually a pretty hard problem. Here is a tougher example just to give you an idea:

if (pepperoni or not olives) and (onions or not pepperoni) and (not olives or not pepperoni):        
  print("pizza")

You don’t need to solve this one. I’m just showing it as an example so you can appreciate how hard this problem can get. You can have any number of variables and any number of clauses, and the problems get pretty hard pretty quickly.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-6.png)

With _n_ toppings, there are 2_`n`_ possible pizzas. If you list them all out and check each one, you get something called a truth table. Here’s the truth table for `pepperoni and (olives or onions)`.

Sometimes you need to list every option, just like the set-covering problem and the traveling salesperson problem! In fact, the SAT problem is just as hard as these two problems. It has a big O run time of O(2_`n`_).

## Hard to solve, quick to verify

We often see problems where finding a solution is much harder than verifying a solution. Suppose I ask you to come up with a sentence that is a palindrome (it reads the same backward and forward) that includes the words _cat_ and _car_. How long do you think it would take you to come up with that sentence?

Now suppose I tell you that I know a sentence like that. Here it is: _Was it a car or a cat I saw?_

It would take you much less time to verify that claim than to come up with your own sentence. Verifying was quicker than solving!

A SAT problem is as hard to solve as the set-covering problem or the traveling salesperson problem, but unlike those problems, verifying a solution is easy. For example, for this question that we gave earlier: (pepperoni or not olives) and (onions or not pepperoni) and (not olives or not pepperoni), here’s a solution:

pepperoni = False
olives = False
onions = False

You can quickly check for yourself that these values will make that boolean formula `true`. Checking those values was faster than solving it yourself!

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-7.png)

The SAT problem is _quick to verify_, so it is in NP.

NP is the class of problems that can be _verified_ in polynomial time. NP problems may or may not be easy to solve, but they are easy to verify. This is different from P.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-8.png)

A problem is in P if it can be _verified and solved_ in polynomial time.

Polynomial time means its big O is not bigger than a polynomial. I won’t define what a polynomial is in this book, but here’s two polynomials.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-9.png)

And here are a couple of examples that are not polynomials.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-10.png)

P is a subset of NP. So NP contains all the problems in P, plus others.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-11.png)

P vs. NP

You may have heard of the famous _P versus NP_ problem. We just saw that the problems in P are both quick to verify and quick to solve. The problems in NP are quick to verify but may or may not be quick to solve. The P versus NP problem asks whether every problem that is quick to verify _is also quick to solve_. If that is the case, P wouldn’t be a subset of NP; P would equal NP.

NP-hard is the next term we will define, but first we need to (briefly) discuss what a reduction is.

## Reductions

What do you do when you have a hard problem? Change the problem to one you can solve! In real life, when we’re faced with a hard problem, it is extremely common to change the problem.

Here’s one you can try right now. How do you multiply two binary numbers? Try multiplying these two binary numbers:

101 * 110

If you’re like me, you didn’t try to figure out how to do the multiplication in binary. You just figured out that `101` is 5 in decimal and `110` is 6, and then you multiplied 5 and 6 instead.

This is called a reduction. You are reducing a problem that you don’t know how to solve to a problem you do know how to solve. This is done all the time in computer science.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-12.png)

## NP-hard

We have already seen three examples of NP-hard problems:

- The set-covering problem
    
- The traveling salesperson problem
    
- The SAT problem
    

(Remember, I mean the _decision_ versions of these problems—all the problems we look at in the rest of this appendix are decision problems.)

The three previously noted problems are NP-hard. We say a problem is NP-hard _if any problem in NP can be reduced to that problem_. This is the definition of NP-hard.

You can also reduce all NP problems to any NP-hard problem. For example, you can reduce all NP problems to SAT.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-13.png)

One extra requirement is that you need to be able to reduce all these problems _in polynomial time_. That “in polynomial time” is important because you don’t want the reducing part to be the bottleneck. Any NP problem can be reduced to SAT in polynomial time, so it is NP-hard.

Since any problem in NP can be reduced to any NP-hard problem, a polynomial time solution for any one NP-hard problem gives us a polynomial time solution for every problem in NP!

## NP-complete

We’ve seen two definitions:

- Problems in NP are quick to verify and may or may not be quick to solve.
    
- Problems that are NP-hard are at least as hard as the hardest problems in NP, and any problem in NP can be reduced to a problem in NP-hard.
    

Now here’s my final definition: _a problem is NP-complete if it is both NP and NP-hard._

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_B-14.png)

NP-complete problems are

- Hard to solve (at least right now; if someone proves that P = NP, they would not be)
    
- Easy to verify
    

And any problem in NP can be reduced to a problem that is NP-complete.

Here are the terms we defined in this appendix:

- Decision problems
    
- The SAT problem
    
- P versus NP
    
- Reductions
    
- NP-hard
    
- NP-complete
    

When you see a discussion about NP-complete problems, I hope you’ll feel more confident about what these terms mean!

## Recap

- A problem is in P if it is both quick to solve and quick to verify.
    
- A problem is in NP if it is quick to verify. It may or may not be quick to solve.
    
- If we find a fast (polynomial time) algorithm for every problem in NP, then P = NP.
    
- A problem is NP-hard if any problem in NP can be reduced to that problem.
    
- If a problem is in both NP and NP-hard, it is NP-complete.

# Appendix C. Answers to exercises

Chapter 1

  1.1 Suppose you have a sorted list of 128 names, and you’re searching through it using binary search. What’s the maximum number of steps it would take?

_Answer_: 7.

  1.2 Suppose you double the size of the list. What’s the maximum number of steps now?

_Answer_: 8.

  1.3 You have a name, and you want to find the person’s phone number in the phone book.

_Answer_: O(log _n_).

  1.4 You have a phone number, and you want to find the person’s name in the phone book. (Hint: You’ll have to search through the whole book!)

_Answer_: O(_n_).

  1.5 You want to read the numbers of every person in the phone book.

_Answer_: O(_n_).

  1.6 You want to read the numbers of just the _A_s.

_Answer_: O(_n_). You may think, “I’m only doing this for 1 out of 26 characters, so the run time should be O(_n_/26).” A simple rule to remember is to ignore numbers that are added, subtracted, multiplied, or divided. None of these are correct big O run times: O(_n_ + 26), O(_n_ – 26), O(_n_ * 26), O(_n_/26). They’re all the same as O(_n_)! Why? If you’re curious, flip to “big O notation revisited” in chapter 4 and read up on constants in big O notation (a constant is just a number; 26 was the constant in this question).

Chapter 2

  2.1 Suppose you’re building an app to keep track of your finances.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-1.png)

Every day, you write down everything you spent money on. At the end of the month, you review your expenses and sum up how much you spent. So, you have lots of inserts and a few reads. Should you use an array or a list?

_Answer_: In this case, you’re adding expenses to the list every day and reading all the expenses once a month. Arrays have fast reads and slow inserts. Linked lists have slow reads and fast inserts. Because you’ll be inserting more often than reading, it makes sense to use a linked list. Also, linked lists have slow reads only if you’re accessing random elements in the list. Because you’re reading _every_ element in the list, linked lists will do well on _reads_, too. So a linked list is a good solution to this problem.

  2.2 Suppose you’re building an app for restaurants to take customer orders. Your app needs to store a list of orders. Servers keep adding orders to this list, and chefs take orders off the list and make them. It’s an order queue: servers add orders to the back of the queue, and the chef takes the first order off the queue and cooks it.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-2.png)

Would you use an array or a linked list to implement this queue? (Hint: Linked lists are good for inserts/deletes, and arrays are good for random access. Which one are you going to be doing here?)

_Answer_: A linked list. Lots of inserts are happening (servers adding orders), which linked lists excel at. You don’t need search or random access (what arrays excel at) because the chefs always take the first order off the queue.

  2.3 Let’s run a thought experiment. Suppose Facebook keeps a list of usernames. When someone tries to log in to Facebook, a search is done for their username. If their name is in the list of usernames, they can log in. People log in to Facebook pretty often, so there are a lot of searches through this list of usernames. Suppose Facebook uses binary search to search the list. Binary search needs random access—you need to be able to get to the middle of the list of usernames instantly. Knowing this, would you implement the list as an array or a linked list?

_Answer_: A sorted array. Arrays give you random access—you can get an element from the middle of the array instantly. You can’t do that with linked lists. To get to the middle element in a linked list, you’d have to start at the first element and follow all the links down to the middle element.

  2.4 People sign up for Facebook pretty often, too. Suppose you decided to use an array to store the list of users. What are the downsides of an array for inserts? In particular, suppose you’re using binary search to search for logins. What happens when you add new users to an array?

_Answer_: Inserting into arrays is slow. Also, if you’re using binary search to search for usernames, the array needs to be sorted. Suppose someone named Adit B signs up for Facebook. Their name will be inserted at the end of the array. So you need to sort the array every time a name is inserted!

  2.5 In reality, Facebook uses neither an array nor a linked list to store user information. Let’s consider a hybrid data structure: an array of linked lists. You have an array with 26 slots. Each slot points to a linked list. For example, the first slot in the array points to a linked list containing all the usernames starting with _A_. The second slot points to a linked list containing all the usernames starting with _B_, and so on.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-3.png)

Suppose Adit B signs up for Facebook, and you want to add them to the list. You go to slot 1 in the array, go to the linked list for slot 1, and add Adit B at the end. Now, suppose you want to search for Zakhir H. You go to slot 26, which points to a linked list of all the Z names. Then you search through that list to find Zakhir H.

Compare this hybrid data structure to arrays and linked lists. Is it slower or faster than each for searching and inserting? You don’t have to give big O run times, just whether the new data structure would be faster or slower.

_Answer_: Searching—slower than arrays, faster than linked lists. Inserting—faster than arrays, same amount of time as linked lists. So it’s slower for searching than an array, but faster or the same as linked lists for everything. We’ll talk about another hybrid data structure called a hash table later in the book. This should give you an idea of how you can build up more complex data structures from simple ones.

So what does Facebook really use? It probably uses a dozen different databases with different data structures behind them: hash tables, B-trees, and others. Arrays and linked lists are the building blocks for these more complex data structures.

Chapter 3

  3.1 Suppose I show you a call stack like this.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-4.png)

What information can you give me, just based on this call stack?

_Answer_: Here are some things you could tell me:

The `greet` function is called first, with `name = maggie.`

Then the `greet` function calls the `greet2` function, with `name = maggie`.

At this point, the `greet` function is in an incomplete, suspended state.

The current function call is the `greet2` function.

After this function call completes, the `greet` function will resume.

  3.2 Suppose you accidentally write a recursive function that runs forever. As you saw, your computer allocates memory on the stack for each function call. What happens to the stack when your recursive function runs forever?

_Answer_: The stack grows forever. Each program has a limited amount of space on the call stack. When your program runs out of space (which it eventually will), it will exit with a stack-overflow error.

Chapter 4

  4.1 Write out the code for the earlier sum function.

_Answer_:

**def** sum(list):
  **if** list == []:
    return 0
  **return** list[0] + sum(list[1:])

  4.2 Write a recursive function to count the number of items in a list.

_Answer_:

**def** count(list):
  **if** list == []:
    return 0
  **return** 1 + count(list[1:])

  4.3 Write a recursive function to find the maximum number in a list.

_Answer_:

**def** max(list):
  **if** len(list) == 2:
    **return** list[0] if list[0] > list[1] else list[1]
  sub_max = max(list[1:])
  **return** list[0] if list[0] > sub_max else sub_max

  4.4 Remember binary search from chapter 1? It’s a D&C algorithm, too. Can you come up with the base case and recursive case for binary search?

_Answer_: The base case for binary search is an array with one item. If the item you’re looking for matches the item in the array, you found it! Otherwise, it isn’t in the array.

In the recursive case for binary search, you split the array in half, throw away one half, and call binary search on the other half.

How long would each of these operations take in big O notation?

  4.5 Printing the value of each element in an array.

_Answer_: O(_n_)

  4.6 Doubling the value of each element in an array.

_Answer_: O(_n_)

  4.7 Doubling the value of just the first element in an array.

_Answer_: O(1)

  4.8 Creating a multiplication table with all the elements in the array. So if your array is [2, 3, 7, 8, 10], you first multiply every element by 2, then multiply every element by 3, then by 7, and so on.

_Answer_: O(_n_2)

Chapter 5

Which of these hash functions are consistent?

  5.1 `f(x) = 1`      ← Returns 1 for all input

_Answer_: Consistent

  5.2 `f(x) = rand()`      ← Returns a random number every time

_Answer_: Not consistent

  5.3 `f(x) = next_empty_slot()`      ← Returns the index of the next empty slot in the hash table

_Answer_: Not consistent

  5.4 `f(x) = len(x)`      ← Uses the length of the string as the index

_Answer_: Consistent

Suppose you have these four hash functions that work with strings:

  A.  Return “1” for all input.

  B.  Use the length of the string as the index.

  C.  Use the first character of the string as the index. So, all strings starting with _a_ are hashed together, and so on.

  D.  Map every letter to a prime number: a = 2, b = 3, c = 5, d = 7, e = 11, and so on. For a string, the hash function is the sum of all the characters modulo the size of the hash. For example, if your hash size is 10, and the string is “bag,” the index is 3 + 2 + 17 % 10 = 22 % 10 = 2.

For each of the following examples, which hash functions would provide a good distribution? Assume a hash table size of 10 slots.

  5.5 A phonebook where the keys are names and values are phone numbers. The names are as follows: Esther, Ben, Bob, and Dan.

_Answer_: Hash functions C and D would give a good distribution.

  5.6 A mapping from battery size to power. The sizes are A, AA, AAA, and AAAA.

_Answer_: Hash functions B and D would give a good distribution.

  5.7 A mapping from book titles to authors. The titles are _Maus_, _Fun_ _Home_, and _Watchmen_.

_Answer_: Hash functions B, C, and D would give a good distribution.

Chapter 6

Run the breadth-first search algorithm on each of these graphs to find the solution.

  6.1 Find the length of the shortest path from start to finish.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-5.png)

_Answer_: The shortest path has a length of 2.

  6.2 Find the length of the shortest path from “cab” to “bat.”

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-6.png)

_Answer_: The shortest path has a length of 2.

Here’s a small graph of my morning routine.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-7.png)

  6.3 For these three lists, mark whether each one is valid or invalid.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-8.png)

_Answers_: A—Invalid; B—Valid; C—Invalid.

  6.4 Here’s a larger graph. Make a valid list for this graph.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-9.png)

_Answer_: 1—Wake up; 2—Exercise; 3—Shower; 4—Brush teeth; 5—Get dressed; 6—Pack lunch; 7—Eat breakfast.

  6.5 Which of the following graphs are also trees?

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-10.png)

_Answers_: A—Tree; B—Not a tree; C—Tree. The last example is just a sideways tree. Trees are a subset of graphs. So a tree is always a graph, but a graph may or may not be a tree.

Chapter 9

  9.1 In each of these graphs, what is the weight of the shortest path from Start to Finish?

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-11.png)

_Answers_: A: A—8; B—60; C—4.

Chapter 10

10.1 You work for a furniture company, and you have to ship furniture all over the country. You need to pack your truck with boxes. All the boxes are of different sizes, and you’re trying to maximize the space you use in each truck. How would you pick boxes to maximize space? Come up with a greedy strategy. Will that give you the optimal solution?

_Answer_: A greedy strategy would be to pick the largest box that will fit in the remaining space and repeat until you can’t pack any more boxes. No, this won’t give you the optimal solution.

10.2 You’re going to Europe, and you have seven days to see everything you can. You assign a point value to each item (how much you want to see it) and estimate how long it takes. How can you maximize the point total (seeing all the things you really want to see) during your stay? Come up with a greedy strategy. Will that give you the optimal solution?

_Answer_: Keep picking the activity with the highest point value that you can still do in the time you have left. Stop when you can’t do anything else. No, this won’t give you the optimal solution.

Chapter 11

11.1 Suppose you can steal another item: a mechanical keyboard. It weighs 1 lb and is worth $1,000. Should you steal it?

_Answer_: Yes. Then you could steal the mechanical keyboard, the iPhone, and the guitar, worth a total of $4,500.

11.2 Suppose you’re going camping. You have a knapsack that holds 6 lb, and you can take the following items. They each have a value, and the higher the value, the more important the item is:

- Water, 3 lb, 10
    
- Book, 1 lb, 3
    
- Food, 2 lb, 9
    
- Jacket, 2 lb, 5
    
- Camera, 1 lb, 6
    

What’s the optimal set of items to take on your camping trip?

_Answer_: You should take water, food, and a camera.

11.3 Draw and fill in the grid to calculate the longest common substring between _blue_ and _clues_.

_Answer_:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781633438538/files/OEBPS/Images/image_C-12.png)

Chapter 12

12.1 In the Netflix example, you calculated the distance between two different users using the distance formula. But not all users rate movies the same way. Suppose you have two users, Yogi and Pinky, who have the same taste in movies. But Yogi rates any movie he likes as a 5, whereas Pinky is choosier and reserves the 5s for only the best. They’re well matched, but according to the distance algorithm, they aren’t neighbors. How would you take their different rating strategies into account?

_Answer_: You could use something called normalization. You look at the average rating for each person and use it to scale their ratings. For example, you might notice that Pinky’s average rating is 3, whereas Yogi’s average rating is 3.5. So you bump up Pinky’s ratings a little until her average rating is 3.5 as well. Then you can compare their ratings on the same scale.

12.2 Suppose Netflix nominates a group of “influencers.” For example, Quentin Tarantino and Wes Anderson are influencers on Netflix, so their ratings count for more than a normal user’s. How would you change the recommendations system so it’s biased toward the ratings of influencers?

_Answer_: You could give more weight to the ratings of the influencers when using KNN. Suppose you have three neighbors: Joe, Dave, and Wes Anderson (an influencer). They rated Caddyshack 3, 4, and 5, respectively. Instead of just taking the average of their ratings (3 + 4 + 5 / 3 = 4 stars), you could give Wes Anderson’s rating more weight: 3 + 4 + 5 + 5 + 5 / 5 = 4.4 stars.

12.3 Netflix has millions of users. The earlier example looked at the five closest neighbors for building the recommendations system. Is this too low? Too high?

_Answer_: It’s too low. If you look at fewer neighbors, there’s a bigger chance that the results will be skewed. A good rule of thumb is that if you have _N_ users, you should look at sqrt(_N_) neighbors.
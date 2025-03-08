We’ve seen in the preceding chapters that the primary factor in determining an algorithm’s efficiency is the number of steps it takes.

However, we can’t simply label one algorithm a “22-step algorithm” and another a “400-step algorithm.” This is because the number of steps an algorithm takes cannot be pinned down to a single number. Let’s take linear search, for example. The number of steps linear search takes varies, as it takes as many steps as there are elements in the array. If the array contains 22 elements, linear search takes 22 steps. If the array contains 400 elements, however, linear search takes 400 steps.

The more effective way, then, to quantify the efficiency of linear search is to say that linear search takes N steps for N elements in the array; that is, if an array has N elements, linear search takes N steps. Now, this is a pretty wordy way of expressing this concept.

To help ease communication regarding time complexity, computer scientists have borrowed a concept from the world of mathematics to describe a concise and consistent language around the efficiency of data structures and algorithms. Known as Big O notation, this formalized expression of these concepts allows us to easily categorize the efficiency of a given algorithm and convey it to others.

Once you understand Big O notation, you’ll have the tools to analyze each algorithm going forward in a consistent and concise way—it’s the way the pros do it.

While Big O notation comes from the math world, I’m going to leave out all the mathematical jargon and explain it as it relates to computer science. Additionally, I’m going to begin by explaining Big O notation in simple terms and then continue to refine it as we proceed through this chapter and the next three chapters. It’s not a difficult concept, but it’ll be made even easier if I explain it in chunks over multiple chapters.

## Big O: How Many Steps Relative to N Elements?

Big O achieves consistency by focusing on the number of steps an algorithm takes, but in a specific way. Let’s start off by applying Big O to the algorithm of linear search.

In a worst-case scenario, linear search will take as many steps as there are elements in the array. As we’ve previously phrased it: for N elements in the array, linear search can take up to N steps. The appropriate way to express this in Big O notation is:

O(N)

Some pronounce this as “Big Oh of N.” Others call it “Order of N.” My personal preference, however, is “Oh of N.”

Here’s what the notation means. It expresses the answer to what we’ll call the key question. The key question is this: if there are N data elements, how many steps will the algorithm take? Go ahead and read that sentence again. Then, emblazon it on your forehead, as this is the definition of Big O notation that we’ll be using throughout the rest of this book.

The answer to the key question lies within the parentheses of our Big O expression. O(N) says that the answer to the key question is that the algorithm will take N steps.

Let’s quickly review the thought process for expressing time complexity with Big O notation, again using the example of linear search. First, we ask the key question: if there are N data elements in an array, how many steps will linear search take? Because the answer to this question is that linear search will take N steps, we express this as O(N). For the record, an algorithm that is O(N) is also known as having linear time.

Let’s contrast this with how Big O would express the efficiency of reading from a standard array. As you learned in Chapter 1, [​_Why Data Structures Matter_​](https://learning.oreilly.com/library/view/a-common-sense-guide/9798888650776/f_0013.xhtml#chp.understanding_data_structures), reading from an array takes just one step, no matter how large the array is. To figure out how to express this in Big O terms, we’re going to again ask the key question: if there are N data elements, how many steps will reading from an array take? The answer is that reading takes just one step. So we express this as O(1), which I pronounce “Oh of 1.”

O(1) is interesting, since although our key question revolves around N (“If there are N data elements, how many steps will the algorithm take?”), the answer has nothing to do with N. And that’s actually the whole point: no matter how many elements an array has, reading from the array always takes one step.

And this is why O(1) is considered the “fastest” kind of algorithm. Even as the data increases, an O(1) algorithm doesn’t take any additional steps. The algorithm always takes a constant number of steps no matter what N is. In fact, an O(1) algorithm can also be referred to as having constant time.

So, Where's the Math?

As I mentioned earlier in this book, I’m taking an easy-to-understand approach to the topic of Big O. That’s not the only way to do it; if you were to take a traditional college course on algorithms, you’d probably be introduced to Big O from a mathematical perspective. Big O is originally a concept from mathematics, and therefore, it’s often described in mathematical terms. For example, one way of describing Big O is that it describes the upper bound of the growth rate of a function, or that if a function g(x) grows no faster than a function f(x), then g is said to be a member of O(f). Depending on your mathematics background, that either makes sense or doesn’t help very much. I’ve written this book so that you don’t need as much math to understand the concept.

If you want to dig further into the math behind Big O, check out Introduction to Algorithms by Thomas H. Cormen, Charles E. Leiserson, Ronald L. Rivest, and Clifford Stein (MIT Press, 2009) for a full mathematical explanation. Justin Abrahms also provides a pretty good definition in his article: [https://justin.abrah.ms/computer-science/understanding-big-o-formal-definition.html](https://justin.abrah.ms/computer-science/understanding-big-o-formal-definition.html).

## The Soul of Big O

Now that we’ve encountered O(N) and O(1), we begin to see that Big O notation does more than simply describe the number of steps an algorithm takes, such as with a hard number like 22 or 400. Rather, it’s an answer to that key question on your forehead: if there are N data elements, how many steps will the algorithm take?

While that key question is indeed the strict definition of Big O, there’s actually more to Big O than meets the eye.

Let’s say we have an algorithm that always takes three steps no matter how much data there is. That is, for N elements, the algorithm always takes three steps. How would you express that in terms of Big O?

Based on everything you’ve learned up to this point, you’d probably say that it’s O(3).

However, it’s actually O(1). And that’s because of the next layer of understanding Big O, which I will reveal now.

While Big O is an expression of the number of an algorithm’s steps relative to N data elements, that alone misses the deeper why behind Big O, what I dub the “soul of Big O.”

The soul of Big O is what Big O is truly concerned about: how will an algorithm’s performance change as the data increases?

This is the soul of Big O. Big O doesn’t want to simply tell you how many steps an algorithm takes. It wants to tell you the story of how the number of steps increases as the data changes.

Viewed with this lens, we don’t care very much whether an algorithm is O(1) or O(3). Because both algorithms are the type that aren’t affected by increased data, as their number of steps remains constant, they’re essentially the same kind of algorithm. They’re both algorithms whose steps remain constant irrespective of the data, and we don’t care to make a distinction between the two.

An algorithm that is O(N), on the other hand, is a different type of algorithm. It’s an algorithm whose performance is affected as we increase the data. More specifically, it’s the kind of algorithm whose steps increase in direct proportion to the data as the data increases. This is the story O(N) tells. It tells you about the proportional relationship between the data and the algorithm’s efficiency. It describes exactly how the number of steps increases as the data increases.

Look at how these two types of algorithms are plotted on a graph:

![images/big_o_notation/graph_1.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/big_o_notation/graph_1.png)

Notice that O(N) makes a perfect diagonal line. This is because for every additional piece of data, the algorithm takes one additional step. Accordingly, the more data, the more steps the algorithm will take.

Contrast this with O(1), which is a perfect horizontal line. No matter how much data there is, the number of steps remains constant.

### Deeper into the Soul of Big O

To see why the soul of Big O is so important, let’s go one level deeper. Say we had an algorithm of constant time that always took 100 steps no matter how much data there was. Would you consider that to be more or less performant than an algorithm that is O(N)?

Take a look at the following graph:

![images/big_o_notation/graph_2.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/big_o_notation/graph_2.png)

As the graph depicts, for a data set that is fewer than 100 elements, an O(N) algorithm takes fewer steps than the O(1) 100-step algorithm. At exactly 100 elements, the lines cross, meaning the two algorithms take the same number of steps, namely 100. But here’s the key point: for all arrays greater than 100, the O(N) algorithm takes more steps.

Because there will always be some amount of data at which the tides turn, and O(N) takes more steps from that point until infinity, O(N) is considered to be, on the whole, less efficient than O(1) no matter how many steps the O(1) algorithm actually takes.

The same is true even for an O(1) algorithm that always takes one million steps. As the data increases, there will inevitably reach a point at which O(N) becomes less efficient than the O(1) algorithm and will remain so up toward an infinite amount of data.

### Same Algorithm, Different Scenarios

As you learned in the previous chapters, linear search isn’t always O(N). It’s true that if the item we’re looking for is in the final cell of the array, it will take N steps to find it. But when the item we’re searching for is found in the first cell of the array, linear search will find the item in just one step. So this case of linear search would be described as O(1). If we were to describe the efficiency of linear search in its totality, we’d say that linear search is O(1) in a best-case scenario and O(N) in a worst-case scenario.

While Big O effectively describes both the best- and worst-case scenarios of a given algorithm, Big O notation generally refers to the worst-case scenario unless specified otherwise. This is why most references will describe linear search as being O(N) even though it can be O(1) in a best-case scenario.

This is because a “pessimistic” approach can be a useful tool: knowing exactly how inefficient an algorithm can get in a worst-case scenario prepares us for the worst and may have a strong impact on our choices.

## An Algorithm of the Third Kind

In the previous chapter, you learned that binary search on an ordered array is much faster than linear search on the same array. Let’s now look at how to describe binary search in terms of Big O notation.

We can’t describe binary search as being O(1), because the number of steps increases as the data increases. It also doesn’t fit into the category of O(N), since the number of steps is much fewer than the N data elements. As we have seen, binary search takes only seven steps for an array containing 100 elements.

Binary search, then, seems to fall somewhere in between O(1) and O(N). So what is it?

In Big O terms, we describe binary search as having a time complexity of:

O(log N)

I pronounce this as “Oh of log N.” This type of algorithm is also known as having a time complexity of log time.

Simply put, O(log N) is the Big O way of describing an algorithm that increases one step each time the data is doubled. As you learned in the previous chapter, binary search does just that. You’ll see momentarily why this is expressed as O(log N), but let’s first summarize what you’ve learned so far.

The three types of algorithms you’ve learned about so far can be sorted from most efficient to least efficient as follows:

O(1)

O(log N)

O(N)

Let’s look at a graph that compares the three types:

![images/big_o_notation/graph_three_types_fixed.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/big_o_notation/graph_three_types_fixed.png)

Note how O(log N) curves ever so slightly upward, making it less efficient than O(1) but much more efficient than O(N).

To understand why this algorithm is called O(log N), you need to first understand what logarithms are. If you’re already familiar with this mathematical concept, feel free to skip the next section.

## Logarithms

Let’s examine why algorithms such as binary search are described as O(log N). What is a log, anyway?

Log is shorthand for logarithm. The first thing to note is that logarithms have nothing to do with algorithms, even though the two words look and sound so similar.

Logarithms are the inverse of exponents. Here’s a quick refresher on what exponents are:

23 is the equivalent of:

2 * 2 * 2

This just happens to be 8.

Now, log2 8 is the converse. It means: how many times do you have to multiply 2 by itself to get a result of 8?

Because you have to multiply 2 by itself 3 times to get 8, log2 8 = 3.

Here’s another example:

26 translates to:

2 * 2 * 2 * 2 * 2 * 2 = 64

Because we had to multiply 2 by itself six times to get 64, we have, therefore:

log2 64 = 6.

While the preceding explanation is the “textbook” definition of logarithms, I like to use an alternative way of describing the same concept because many people find that they can wrap their heads around it more easily, especially when it comes to Big O notation.

Another way of explaining log2 8 is this: if we kept dividing 8 by 2 until we ended up with 1, how many 2s would we have in our equation?

8 / 2 / 2 / 2 = 1

In other words, how many times do we need to halve 8 until we end up with 1? In this example, it takes us three times. Therefore,

log2 8 = 3.

Similarly, we could explain log2 64 as: how many times do we need to halve 64 until we end up with 1?

64 / 2 / 2 / 2 / 2 / 2 / 2 = 1

Since there are six 2s, log2 64 = 6.

Now that you understand what logarithms are, the meaning behind O(log N) will become clear.

## O(log N) Explained

Let’s bring this all back to Big O notation. In computer science, whenever we say O(log N), it’s actually shorthand for saying O(log2 N). We just omit that small 2 for convenience.

Recall that Big O notation resolves the key question: if there are N data elements, how many steps will the algorithm take?

O(log N) means that for N data elements, the algorithm would take log2 N steps. If there are 8 elements, the algorithm would take three steps, since log2 8 = 3.

Said another way, if we keep dividing the 8 elements in half, it would take us three steps until we end up with 1 element.

This is exactly what happens with binary search. As we search for a particular item, we keep dividing the array’s cells in half until we narrow it down to the correct number.

Said simply: O(log N) means the algorithm takes as many steps as it takes to keep halving the data elements until we remain with 1.

The following table demonstrates a striking difference between the efficiencies of O(N) and O(log N):

|N Elements|O(N)|O(log N)|
|---|---|---|
|8|8|3|
|16|16|4|
|32|32|5|
|64|64|6|
|128|128|7|
|256|256|8|
|512|512|9|
|1024|1024|10|

While the O(N) algorithm takes as many steps as there are data elements, the O(log N) algorithm takes just one additional step each time the data is doubled.

In future chapters, you’ll encounter algorithms that fall under categories of Big O notation other than the three you’ve learned about so far. But in the meantime, let’s apply these concepts to some examples of everyday code.

## Practical Examples

Here’s some typical Python code that prints all the items from a list:

|   |   |
|---|---|
|​|things = [​_'apples'_​, ​_'baboons'_​, ​_'cribs'_​, ​_'dulcimers'_​]|
|​||
|​|​**for**​ thing ​**in**​ things:|
|​|​**print**​(​_"Here's a thing: "_​ + thing)|

How would we describe the efficiency of this algorithm in Big O notation?

The first thing to realize is that this is an example of an algorithm. While it may not be fancy, any code that does anything at all is technically an algorithm—it’s a particular process for solving a problem. In this case, the problem is that we want to print all the items from a list. The algorithm we use to solve this problem is a for loop containing a print statement.

To break this down, we need to analyze how many steps this algorithm takes. In this case, the main part of the algorithm—the for loop—takes four steps. In this example, there are four things in the list, and we print each one out a single time.

However, the number of steps isn’t constant. If the list contained ten elements, the for loop would take ten steps. Since this for loop takes as many steps as there are elements, we’d say that this algorithm has an efficiency of O(N).

The next example is a simple Python-based algorithm for determining whether a number is prime:

|   |   |
|---|---|
|​|​**def**​ ​**is_prime**​(number):|
|​|​**for**​ i ​**in**​ range(2, number):|
|​|​**if**​ number % i == 0:|
|​|​**return**​ False|
|​||
|​|​**return**​ True|

The preceding code accepts a number as an argument and begins a for loop in which we divide the number by every integer from 2 up to (but not including) that number and see if there’s a remainder. If there’s no remainder, we know that the number is not prime and we immediately return False. If we make it all the way up to the number and always find a remainder, then we know that the number is prime and we return True.

In this case, the key question is slightly different than in the previous examples. In the previous examples, our key question asked how many steps the algorithm would take if there were N data elements in an array. Here, we’re not dealing with an array, but we are dealing with a number that we pass into this function. Depending on the number we pass in, this will affect how many times the function’s loop runs.

In this case, then, our key question will be: when passing in the number N, how many steps will the algorithm take?

If we pass the number 7 into is_prime, the for loop runs about 7 times. (It technically runs 5 times, since it starts at 2 and ends right before the actual number.) For the number 101, the loop runs about 101 times. Because the number of steps increases in lockstep with the number passed into the function, this is a classic example of O(N).

Again, the key question here dealt with a different kind of N, since our primary piece of data was a number rather than an array. We’ll get more practice in identifying our Ns as we progress through the future chapters.

## Wrapping Up

With Big O notation, we have a consistent system that allows us to compare any two algorithms. With it, we’ll be able to examine real-life scenarios and choose between competing data structures and algorithms to make our code faster and able to handle heavier loads.

In the next chapter, we’ll encounter a real-life example in which we use Big O notation to speed up our code significantly.

## Exercises

The following exercises provide you with the opportunity to practice with Big O notation. The solutions to these exercises are found in the section [​_Chapter 3_​](https://learning.oreilly.com/library/view/a-common-sense-guide/9798888650776/f_0208.xhtml#big.o.notation.solutions).

1. Use Big O notation to describe the time complexity of the following function that determines whether a given year is a leap year:
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**is_leap_year**​(year):|
    |​||
    |​|​**if**​ year % 100 == 0:|
    |​|​**if**​ year % 400 == 0:|
    |​|​**return**​ False|
    |​|​**else**​:|
    |​|​**return**​ True|
    |​||
    |​|​**return**​ year % 4 == 0|
    
2. Use Big O notation to describe the time complexity of the following function that sums up all the numbers from a given array:
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**array_sum**​(array):|
    |​|sum = 0|
    |​||
    |​|​**for**​ number ​**in**​ array:|
    |​|sum += number|
    |​||
    |​|​**return**​ sum|
    
3. The following function is based on the age-old analogy used to describe the power of compounding interest:
    
    Imagine you have a chessboard, and put a single grain of rice on one square. On the second square, you put two grains of rice, since that is double the amount of rice on the previous square. On the third square, you put four grains. On the fourth square, you put eight grains, and on the fifth square, you put sixteen grains, and so on.
    
    The following function calculates which square you’ll need to place a certain number of rice grains. For example, for sixteen grains, the function will return 5, since you will place the sixteen grains on the fifth square.
    
    Use Big O notation to describe the time complexity of this function, which is below:
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**chessboard_space**​(number_of_grains):|
    |​|chessboard_spaces = 1|
    |​|placed_grains = 1|
    |​||
    |​|​**while**​ placed_grains < number_of_grains:|
    |​|placed_grains *= 2|
    |​|chessboard_spaces += 1|
    |​||
    |​|​**return**​ chessboard_spaces|
    
4. The following function accepts an array of strings and returns a new array that only contains the strings that start with the character "a". Use Big O notation to describe the time complexity of the function:
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**select_a_strings**​(array):|
    |​|new_array = []|
    |​||
    |​|​**for**​ string ​**in**​ array:|
    |​|​**if**​ string[0] == ​_"a"_​:|
    |​|new_array.append(string)|
    |​||
    |​|​**return**​ new_array|
    
5. The following function calculates the median from an ordered array. Describe its time complexity in terms of Big O notation:
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**median**​(array):|
    |​|​**if**​ ​**not**​ array:|
    |​|​**return**​ None|
    |​||
    |​|middle = len(array) // 2|
    |​||
    |​|​_# If array has even amount of numbers:_​|
    |​|​**if**​ len(array) % 2 == 0:|
    |​|​**return**​ (array[middle - 1] + array[middle]) / 2.0|
    |​|​**else**​: ​_# If array has odd amount of numbers:_​|
    |​|​**return**​ array[middle]|
Search for books, courses, events, and more

## Bubble Sort

Before jumping into our practical problem, though, we need to first look at a new category of algorithmic efficiency in the world of Big O. To demonstrate it, we’ll get to use one of the classic algorithms of computer-science lore.

Sorting algorithms have been the subject of extensive research in computer science, and tens of such algorithms have been developed over the years. They all solve the following problem:

Given an array of unsorted values, how can we sort them so that they end up in ascending order?

In this chapter and those following, we’re going to encounter a number of these sorting algorithms. Some of the first ones you’ll learn about are known as simple sorts, in that they are easy to understand but are not as efficient as some of the faster sorting algorithms out there.

Bubble Sort is a basic sorting algorithm and follows these steps:

1. Point to two consecutive values in the array. (Initially, we start by pointing to the array’s first two values.) Compare the first item with the second one:
    
    ![images/speeding_up_your_code_with_big_o/bubble_sort_1.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_1.png)
    
2. If the two items are out of order (in other words, the left value is greater than the right value), swap them (if they already happen to be in the correct order, do nothing for this step):
    
    ![images/speeding_up_your_code_with_big_o/bubble_sort_2.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_2.png)
    
    ![images/speeding_up_your_code_with_big_o/bubble_sort_3.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_3.png)
    
3. Move the “pointers” one cell to the right:
    
    ![images/speeding_up_your_code_with_big_o/bubble_sort_4.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_4.png)
    
4. Repeat Steps 1 through 3 until we reach the end of the array, or if we reach the values that have already been sorted. (This will make more sense in the walk-through that follows.) At this point, we’ve completed our first pass-through of the array—we “passed through” the array by pointing to each of its values until we reached the end.
    
5. We then move the two pointers back to the first two values of the array and execute another pass-through of the array by running Steps 1 through 4 again. We keep on executing these pass-throughs until we have a pass-through in which we did not perform any swaps. When this happens, it means our array is fully sorted and our work is done.
6. ## Bubble Sort in Action

Let’s walk through a complete example of Bubble Sort.

Assume we want to sort the array [4, 2, 7, 1, 3]. It’s currently out of order, and we want to produce an array that contains the same values in ascending order.

Let’s begin the first pass-through:

This is our starting array:

![images/speeding_up_your_code_with_big_o/bubble_sort_5.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_5.png)

Step 1: First, we compare the 4 and the 2:

![images/speeding_up_your_code_with_big_o/bubble_sort_6.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_6.png)

Step 2: They’re out of order, so we swap them:

![images/speeding_up_your_code_with_big_o/bubble_sort_7.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_7.png)

![images/speeding_up_your_code_with_big_o/bubble_sort_8.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_8.png)

Step 3: Next, we compare the 4 and the 7:

![images/speeding_up_your_code_with_big_o/bubble_sort_9.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_9.png)

They’re in the correct order, so we don’t need to perform a swap.

Step 4: We now compare the 7 and the 1:

![images/speeding_up_your_code_with_big_o/bubble_sort_10.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_10.png)

Step 5: They’re out of order, so we swap them:

![images/speeding_up_your_code_with_big_o/bubble_sort_11.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_11.png)

![images/speeding_up_your_code_with_big_o/bubble_sort_12.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_12.png)

Step 6: We compare the 7 and the 3:

![images/speeding_up_your_code_with_big_o/bubble_sort_13.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_13.png)

Step 7: They’re out of order, so we swap them:

![images/speeding_up_your_code_with_big_o/bubble_sort_14.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_14.png)

![images/speeding_up_your_code_with_big_o/bubble_sort_15.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_15.png)

We now know for a fact that the 7 is in its correct position within the array because we kept moving it along to the right until it reached its proper place. The previous diagram has little lines surrounding the 7 to indicate that the 7 is officially in its correct position.

This is actually the reason why this algorithm is called Bubble Sort: in each pass-through, the highest unsorted value “bubbles” up to its correct position.

Because we made at least one swap during this pass-through, we need to conduct another pass-through.

We begin the second pass-through:

Step 8: We compare the 2 and the 4:

![images/speeding_up_your_code_with_big_o/bubble_sort_17.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_17.png)

They’re in the correct order, so we can move on.

Step 9: We compare the 4 and the 1:

![images/speeding_up_your_code_with_big_o/bubble_sort_18.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_18.png)

Step 10: They’re out of order, so we swap them:

![images/speeding_up_your_code_with_big_o/bubble_sort_19.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_19.png)

![images/speeding_up_your_code_with_big_o/bubble_sort_20.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_20.png)

Step 11: We compare the 4 and the 3:

![images/speeding_up_your_code_with_big_o/step_11.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/step_11.png)

Step 12: They’re out of order, so we swap them:

![images/speeding_up_your_code_with_big_o/bubble_sort_21.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_21.png)

![images/speeding_up_your_code_with_big_o/bubble_sort_22.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_22.png)

We don’t have to compare the 4 and the 7 because we know that the 7 is already in its correct position from the previous pass-through. And now we also know that the 4 has bubbled up to its correct position as well. This concludes our second pass-through.

Because we made at least one swap during this pass-through, we need to conduct another pass-through.

We begin the third pass-through:

Step 13: We compare the 2 and the 1:

![images/speeding_up_your_code_with_big_o/bubble_sort_23.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_23.png)

Step 14: They’re out of order, so we swap them:

![images/speeding_up_your_code_with_big_o/bubble_sort_24.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_24.png)

![images/speeding_up_your_code_with_big_o/bubble_sort_24b.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_24b.png)

Step 15: We compare the 2 and the 3:

![images/speeding_up_your_code_with_big_o/bubble_sort_25.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_25.png)

They’re in the correct order, so we don’t need to swap them.

We now know that the 3 has bubbled up to its correct spot:

![images/speeding_up_your_code_with_big_o/bubble_sort_26.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_26.png)

Since we made at least one swap during this pass-through, we need to perform another one.

And so begins the fourth pass-through:

Step 16: We compare the 1 and the 2:

![images/speeding_up_your_code_with_big_o/bubble_sort_27.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_27.png)

Because they’re in order, we don’t need to swap. We can end this pass-through, since all the remaining values are already correctly sorted.

Now that we’ve made a pass-through that didn’t require any swaps, we know that our array is completely sorted:

![images/speeding_up_your_code_with_big_o/bubble_sort_28.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_28.png)

### Code Implementation: Bubble Sort

Here’s an implementation of Bubble Sort in Python:

|   |   |
|---|---|
|​|​**def**​ ​**bubble_sort**​(array):|
|​|unsorted_until_index = len(array) - 1|
|​|sorted = False|
|​||
|​|​**while**​ ​**not**​ sorted:|
|​|sorted = True|
|​|​**for**​ i ​**in**​ range(unsorted_until_index):|
|​|​**if**​ array[i] > array[i+1]:|
|​|array[i], array[i+1] = array[i+1], array[i]|
|​|sorted = False|
|​|unsorted_until_index -= 1|
|​||
|​|​**return**​ array|

To use this function, we can pass an unsorted array to it, like so:

|   |   |
|---|---|
|​|​**print**​(bubble_sort([65, 55, 45, 35, 25, 15, 10]))|

This function will then return the sorted array.

Let’s break the function down line by line to see how it works. I’ll explain each line by first providing the explanation, followed by the line of code itself.

The first thing we do is create a variable called unsorted_until_index. This keeps track of the rightmost index of the array that has not yet been sorted. When we first start the algorithm, the array is completely unsorted, so we initialize this variable to be the final index in the array:

|   |   |
|---|---|
|​|unsorted_until_index = len(array) - 1|

We also create a variable called sorted that will keep track of whether the array is fully sorted. Of course, when our code first runs, it isn’t, so we set it to False:

|   |   |
|---|---|
|​|sorted = False|

We begin a while loop that continues to run as long as the array is not sorted. Each round of this loop represents a pass-through of the array:

|   |   |
|---|---|
|​|​**while**​ ​**not**​ sorted:|

Next, we preliminarily establish sorted to be True:

|   |   |
|---|---|
|​|sorted = True|

The approach here is that in each pass-through, we’ll assume the array is sorted until we encounter a swap, in which case we’ll change the variable back to False. If we get through an entire pass-through without having to make any swaps, sorted will remain True, and we’ll know that the array is completely sorted.

Within the while loop, we begin a for loop in which we point to each pair of values in the array. We use the variable i as our first pointer, and it starts from the beginning of the array and goes until the index that hasn’t yet been sorted:

|   |   |
|---|---|
|​|​**for**​ i ​**in**​ range(unsorted_until_index):|

Within this loop, we compare each pair of adjacent values and swap those values if they’re out of order. We also change sorted to False if we have to make a swap:

|   |   |
|---|---|
|​|​**for**​ i ​**in**​ range(unsorted_until_index):|
|​|​**if**​ array[i] > array[i+1]:|
|​|array[i], array[i+1] = array[i+1], array[i]|
|​|sorted = False|

At the end of each pass-through, we know that the value we bubbled up all the way to the right is now in its correct position. Because of this, we decrement the unsorted_until_index by 1, since the index it was already pointing to is now sorted:

|   |   |
|---|---|
|​|unsorted_until_index -= 1|

The while loop ends once sorted is True, meaning the array is completely sorted. Once this is the case, we return the sorted array:

|     |                    |
| --- | ------------------ |
| ​   | ​**return**​ array |
## The Efficiency of Bubble Sort

The Bubble Sort algorithm contains two significant kinds of steps:

- Comparisons: two numbers are compared with one another to determine which is greater.
    
- Swaps: two numbers are swapped with one another to sort them.
    

Let’s start by determining how many comparisons take place in Bubble Sort.

Our example array has five elements. Looking back, you can see that in our first pass-through, we had to make four comparisons between sets of two numbers.

In our second pass-through, we only had to make three comparisons. This is because we didn’t have to compare the final two numbers, since we knew that the final number was in the correct spot due to the first pass-through.

In our third pass-through, we made two comparisons, and in our fourth pass-through, we made just one comparison.

So that’s:

4 + 3 + 2 + 1 = 10 comparisons.

To put this in a way that would hold true for arrays of all sizes, we’d say that for N elements, we make

(N - 1) + (N - 2) + (N - 3) … + 1 comparisons.

Now that we’ve analyzed the number of comparisons that take place in Bubble Sort, let’s analyze the swaps.

In a worst-case scenario, where the array is sorted in descending order (the exact opposite of what we want), we’d actually need a swap for each comparison. So we’d have 10 comparisons and 10 swaps in such a scenario for a grand total of 20 steps.

Let’s look at the big picture. With an array containing five values in reverse order, we make 4 + 3 + 2 + 1 = 10 comparisons. Along with the 10 comparisons, we also have 10 swaps, totaling 20 steps.

For such an array with 10 values, we get 9 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1 = 45 comparisons, and another 45 swaps. That’s a total of 90 steps.

With an array containing 20 values, we’d have:

19 + 18 + 17 + 16 + 15 + 14 + 13 + 12 + 11 + 10 + 9 + 8 + 7 + 6 + 5 + 4 + 3 + 2 + 1 = 190 comparisons, and approximately 190 swaps, for a total of 380 steps.

Notice the inefficiency here. As the number of elements increases, the number of steps grows exponentially. (In technical math terms, we’d actually say that it grows quadratically.) We can see this clearly in the following table:

|N Data Elements|Max # of Steps|
|---|---|
|5|20|
|10|90|
|20|380|
|40|1560|
|80|6320|

If you look at the growth of steps as N increases, you’ll see that it’s growing by approximately N2. Take a look at the following table:

|N Data Elements|# of Bubble Sort Steps|N2|
|---|---|---|
|5|20|25|
|10|90|100|
|20|380|400|
|40|1560|1600|
|80|6320|6400|

Let’s express the time complexity of Bubble Sort with Big O notation. Remember, Big O always answers the key question: if there are N data elements, how many steps will the algorithm take?

Because for N values, Bubble Sort takes N2 steps, in Big O we say that Bubble Sort has an efficiency of O(N2).

O(N2) is considered to be a relatively inefficient algorithm, since as the data increases, the steps increase dramatically. Look at this graph, which compares O(N2) against the faster O(N):

![images/speeding_up_your_code_with_big_o/bubble_sort_30.png](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9798888650776/files/images/speeding_up_your_code_with_big_o/bubble_sort_30.png)

Note how O(N2) curves sharply upward in terms of number of steps as the data grows. Compare this with O(N), which plots along a simple, diagonal line.

One last note: O(N2) is also referred to as quadratic time.

## A Quadratic Problem

Here’s a practical example of where we can replace a slow O(N2) algorithm with a speedy O(N) one.

Let’s say you’re working on a Python application that analyzes the ratings people give to products, where users leave ratings from 0 to 10. Specifically, you’re writing a function that checks whether an array of ratings contains any duplicate numbers. This will be used in more complex calculations in other parts of the software.

For example, the array [1, 5, 3, 9, 1, 4] has two instances of the number 1, so we’d return True to indicate that the array has a case of duplicate numbers.

One of the first approaches that may come to mind is the use of nested loops, as follows:

|   |   |
|---|---|
|​|​**def**​ ​**has_duplicate_value**​(array):|
|​|​**for**​ i ​**in**​ range(len(array)):|
|​|​**for**​ j ​**in**​ range(len(array)):|
|​|​**if**​ (i != j) ​**and**​ (array[i] == array[j]):|
|​|​**return**​ True|
|​||
|​|​**return**​ False|

In this function, we iterate through each value of the array using the variable i. As we focus on each value in i, we then run a second loop that looks through all the values in the array—using j—and checks if the values at positions i and j are the same. If they are, it means we’ve encountered duplicate values and we return True. If we get through all of the looping and we haven’t encountered any duplicates, we return False, since we know that there are no duplicates in the array.

While this certainly works, is it efficient? Now that we know a bit about Big O notation, let’s take a step back and see what Big O would say about this function.

Remember that Big O expresses how many steps the algorithm takes relative to N data values. To apply this to our situation, we’d ask ourselves: for N values in the array provided to our has_duplicate_value function, how many steps would our algorithm take in a worst-case scenario?

To answer the preceding question, we need to analyze what steps our function takes as well as what the worst-case scenario would be.

The preceding function has one type of step, namely comparisons. It repeatedly compares array[i] and array[j] to see if they are equal and therefore represent a duplicate pair. In a worst-case scenario, the array contains no duplicates, which would force our code to complete all of the loops and exhaust every possible comparison before returning False.

Based on this, we can conclude that for N values in the array, our function would perform N2 comparisons. This is because we perform an outer loop that must iterate N times to get through the entire array, and for each iteration, we must iterate another N times with our inner loop. That’s N steps * N steps, which is N2 steps, leaving us with an algorithm of O(N2).

We can actually prove that our function takes N2 steps by adding some code to our function that tracks the algorithm’s number of steps:

|   |   |
|---|---|
|​|​**def**​ ​**has_duplicate_value**​(array):|
|​|steps = 0 ​_# count of steps_​|
|​|​**for**​ i ​**in**​ range(len(array)):|
|​|​**for**​ j ​**in**​ range(len(array)):|
|​|steps += 1 ​_# increment number of steps_​|
|​|​**if**​ (i != j) ​**and**​ (array[i] == array[j]):|
|​|​**return**​ True|
|​||
|​|​**print**​(steps) ​_# print number of steps if no duplicates_​|
|​|​**return**​ False|

This added code will print the number of steps taken when there are no duplicates. If we run has_duplicate_value([1, 4, 5, 2, 9]), for example, we’ll see an output of 25 in the Python console, indicating that there were twenty-five comparisons for the five elements in the array. If we test this for other values, we’ll see that the output is always the size of the array squared. This is classic O(N2).

Very often (but not always), when an algorithm nests one loop inside another, the algorithm is O(N2). So whenever you see a nested loop, O(N2) alarm bells should go off in your head.

Now, the fact that our function is O(N2) should give us pause. This is because O(N2) is considered a relatively slow algorithm. Whenever you encounter a slow algorithm, it’s worth spending some time to consider whether there are any faster alternatives. There may not be any better alternatives, but let’s first make sure.

## A Linear Solution

What follows is another implementation of the has_duplicate_value function that doesn’t rely on nested loops. It’s a bit clever, so let’s first look at how it works and then we’ll see if it’s any more efficient than our first implementation.

|   |   |
|---|---|
|​|​**def**​ ​**has_duplicate_value**​(array):|
|​|existing_numbers = [0] * 11|
|​||
|​|​**for**​ i ​**in**​ range(len(array)):|
|​|​**if**​ existing_numbers[array[i]] == 1:|
|​|​**return**​ True|
|​|​**else**​:|
|​|existing_numbers[array[i]] = 1|
|​||
|​|​**return**​ False|

Here’s what this function does. It creates an array called existing_numbers, which starts out as an array containing eleven zeroes. We’re ensuring that our array has at least eleven slots so we can keep track of the eleven possible ratings users can leave (0 to 10).

Then we use a loop to check each number in the array. As it encounters each number, it places an arbitrary value (we’ve chosen to use a 1) in the existing_numbers array at the index of the number we’re encountering.

For example, let’s say our input array is [3, 5, 8]. When we encounter the 3, we place a 1 at index 3 of existing_numbers. So the existing_numbers array will now be the rough equivalent of this:

|   |   |
|---|---|
|​|[0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]|

There’s now a 1 at index 3 of existing_numbers, to indicate and remember for the future that we’ve already encountered a 3 in our given array.

When our loop then encounters the 5 from the given array, it adds a 1 to index 5 of existing_numbers:

|   |   |
|---|---|
|​|[0, 0, 0, 1, 0, 1, 0, 0, 0, 0, 0]|

Finally, when we reach the 8, existing_numbers will now look like this:

|   |   |
|---|---|
|​|[0, 0, 0, 1, 0, 1, 0, 0, 1, 0, 0]|

Essentially, we’re using the indexes of existing_numbers to remember which numbers from the array we’ve seen so far.

Now, here’s the real trick. Before the code stores a 1 in the appropriate index, it first checks to see whether that index already has a 1 as its value. If it does, this means we’ve already encountered that number, meaning we found a duplicate. If this is the case, we simply return True and cut the function short. If we get to the end of the loop without having returned True, it means there are no duplicates and we return False.

To determine the efficiency of this new algorithm in terms of Big O, we once again need to determine the number of steps the algorithm takes in a worst-case scenario.

Here, the significant type of step is looking at each number and checking whether the value of its index in existing_numbers is a 1:

|   |   |
|---|---|
|​|​**if**​ existing_numbers[array[i]] == 1:|

(In addition to the comparisons, we also make insertions into the existing_numbers array, but we’re considering that kind of step trivial in this analysis. More on this in the next chapter.)

In terms of the worst-case scenario, such a scenario would occur when the array contains no duplicates, in which case our function must complete the entire loop.

This new algorithm appears to make N comparisons for N data elements. This is because there’s only one loop, and it simply iterates for as many numbers as there are in the array. We can test out this theory by tracking the steps in the Python console:

|   |   |
|---|---|
|​|​**def**​ ​**has_duplicate_value**​(array):|
|​|steps = 0|
|​|existing_numbers = [0] * 11|
|​||
|​|​**for**​ i ​**in**​ range(len(array)):|
|​|steps += 1|
|​|​**if**​ existing_numbers[array[i]] == 1:|
|​|​**return**​ True|
|​|​**else**​:|
|​|existing_numbers[array[i]] = 1|
|​||
|​|​**print**​(steps)|
|​|​**return**​ False|

If we run has_duplicate_value([1, 4, 5, 2, 9]) now, we’ll see that the output in the Python console is 5, which is the same as the size of our array. We’d find this to be true across arrays of all sizes. This algorithm, then, is O(N).

We know that O(N) is much faster than O(N2), so by using this second approach, we’ve optimized our has_duplicate_value function significantly. This is a huge speed boost.

(One disadvantage with this new implementation is that this approach will consume more memory than the first approach. Don’t worry about this for now; we’ll discuss this at length in Chapter 19, [​_Dealing with Space Constraints_​](https://learning.oreilly.com/library/view/a-common-sense-guide/9798888650776/f_0189.xhtml#chp.dealing_with_space_constraints).)

## Wrapping Up

It’s clear that having a solid understanding of Big O notation can enable you to identify slow code and select the faster of two competing algorithms.

However, in some situations Big O notation will have us believe that two algorithms have the same speed, while one is actually faster. In the next chapter, you’re going to learn how to evaluate the efficiencies of various algorithms even when Big O isn’t nuanced enough to do so.

## Exercises

The following exercises provide you with the opportunity to practice with speeding up your code. The solutions to these exercises are found in the section [​_Chapter 4_​](https://learning.oreilly.com/library/view/a-common-sense-guide/9798888650776/f_0209.xhtml#speeding.up.your.code.with.big.o.solutions).

1. Replace the question marks in the following table to describe how many steps occur for a given number of data elements across various types of Big O:
    
    |N Elements|O(N)|O(log N)|O(N2)|
    |---|---|---|---|
    |100|100|?|?|
    |2000|?|?|?|
    
2. If we have an O(N2) algorithm that processes an array and find that it takes 256 steps, what is the size of the array?
    
3. Use Big O notation to describe the time complexity of the following function. It finds the greatest product of any pair of two numbers within a given array:
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**greatest_product**​(array):|
    |​|​**if**​ len(array) < 2:|
    |​|​**return**​ None|
    |​||
    |​|greatest_product_so_far = array[0] * array[1]|
    |​||
    |​|​**for**​ index_i, value_i ​**in**​ enumerate(array):|
    |​|​**for**​ index_j, value_j ​**in**​ enumerate(array):|
    |​|​**if**​ (index_i != index_j ​**and**​|
    |​|value_i * value_j > greatest_product_so_far):|
    |​|greatest_product_so_far = value_i * value_j|
    |​||
    |​|​**return**​ greatest_product_so_far|
    
4. The following function finds the greatest single number within an array, but it has an efficiency of O(N2). Rewrite the function so that it becomes a speedy O(N):
    
    |   |   |
    |---|---|
    |​|​**def**​ ​**greatest_number**​(array):|
    |​|​**if**​ ​**not**​ array:|
    |​|​**return**​ None|
    |​||
    |​|​**for**​ i ​**in**​ array:|
    |​|​_# Assume for now that i is the greatest:_​|
    |​|is_i_the_greatest = True|
    |​||
    |​|​**for**​ j ​**in**​ array:|
    |​|​_# If we find another value that is greater than i,_​|
    |​|​_# i is not the greatest:_​|
    |​|​**if**​ j > i:|
    |​|is_i_the_greatest = False|
    |​||
    |​|​_# If, by the time we checked all the other numbers, i_​|
    |​|​_# is still the greatest, it means that i is the greatest number:_​|
    |​|​**if**​ is_i_the_greatest:|
    |​|​**return**​ i|
    

Copyright © 2024, The Pragmatic Bookshelf.
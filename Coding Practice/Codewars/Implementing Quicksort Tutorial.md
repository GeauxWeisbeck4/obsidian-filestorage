---
tags:
  - codewars
  - coding-practice
  - algorithms
  - quicksort
  - tutorial
  - sorting
  - recursion
---
# [Implementing Quicksort Tutorial](https://www.codewars.com/kata/6641778ddab67c48a794387d)

### Task:

Program a particular implementation of the Quicksort sorting algorithm, as described below. For simplicity, we will consider sorting integers in ascending order.

### Input:

A list of integers of size n, with 1 ≤ n ≤ 1000.

### Output:

A list of snapshots documenting the contents of the list as the algorithm executes. Each snapshot differs from the previous one by a swap of two elements. The final snapshot must be the sorted list. The snapshots allow the tests to confirm that the list is sorted in the way described below.

### Algorithm Outline:

Quicksort starts by picking an element in the list, called the _pivot_. The pivot in our implementation is always the first element. The algorithm partitions the list into two sublists, the elements smaller and larger than the pivot, and places the pivot between them. It repeats this process on the two sublists until the entire list is sorted.

Partitioning is done by using indexes `**i**` and `**j**` to find elements that should be swapped. Index `**i**` starts at the position to the right of the pivot, and moves right. Index `**j**` starts at the end of the list, moving left. If `**i**` finds an element bigger than the pivot and `**j**` finds one smaller than the pivot, those elements are swapped, and we take a snapshot. _Elements that equal the pivot are ignored_. When `**i**` and `**j**` cross, the algorithm ends the partitioning process by swapping the pivot with the element at position `**j**`.

### Example: `[30, 50, 5, 30, 51, 10, 20, 99, 18, 30, 100, 33]`

Phase 1 - Partition the list.

In this example the _pivot_ is 30. `**i**` starts at the 50, which is bigger than the pivot. Meanwhile `**j**` start at the 33, but it only finds a element smaller than the pivot when it reaches the 18.

```
[30, 50, 5, 30, 51, 10, 20, 99, 18, 30, 100, 33]  
      i                          j
```

The 18 and 50 are swapped. **The 1st snapshot** is `[30, **18**, 5, 30, 51, 10, 20, 99, **50**, 30, 100, 33]`.

`**i**` and `**j**` continue. The next element bigger than the pivot that `**i**` encounters is the 51. The next element smaller than the pivot that `**j**` encounters is the 20. After swapping these, **the 2nd snapshot** is `[30, 18, 5, 30, **20**, 10, **51**, 99, 50, 30, 100, 33]`.

This process continues as long as `**i** ≤ **j**`. When they cross, the situation is:

```
[30, 18, 5, 30, 20, 10, 51, 99, 50, 30, 100, 33] 
                    j   i
```

After the `**i**, **j**` loop terminates, the algorithm ends the partitioning process by swapping the pivot with the element at position `**j**`. In this case this is the 10. **The 3rd snapshot** is `[**10**, 18, 5, 30, 20, **30**, 51, 99, 50, 30, 100, 33]`.

What has been achieved? All elements to the left of the pivot are smaller or equal to it, while all elements to the right of the pivot are greater or equal to it. The pivot 30 is in the correct sorted position. We have partitioned the list into two sublists with the pivot between them. Now we repeat the process on the two sublists `[10, 18, 5, 30, 20]` and `[51, 99, 50, 30, 100, 33]`.

NOTE: In certain cases one of the sublists might contain only a single element, if `**i**` and `**j**` meet at either the beginning or end of the list.

Phase 2 - Partition the first sublist.

Consider the first sublist `[10, 18, 5, 30, 20]`. Set the pivot to the first element 10. `**i**` and `**j**` swap the bigger 18 with the smaller 5, so the **4th snapshot is** `[10, **5**, **18**, 30, 20, 30, 51, 99, 50, 30, 100, 33]`. They don't swap any more before they cross. Then we swap the pivot with the element at position `**j**`, giving **the 5th snapshot** `[**5**, **10**, 18, 30, 20, 30, 51, 99, 50, 30, 100, 33]`. We have partitioned `[10, 18, 5, 30, 20]` into `[5]` and `[18, 30, 20]` with the pivot 10 between them.

Phase 3 - Continue partitioning until the first sublist is sorted.

Repeat the process on these two sublists. Obviously the one element list `[5]` is sorted. The second sublist `[18, 30, 20]` requires two steps. First 18 is picked as pivot. Because it's the _minimum_ element in the sublist, `**j**` moves all the way to the front to the list while **i** doesn't move, and no swaps occur. Since `**j**` ends up at position 0, swapping the pivot with the element at position `**j**` has no effect. The pivot 18 is in the correct position already, and the sub-sublist `[30, 20]` is generated. Now 30 is picked as pivot and gets swapped with 20, creating **the 6th snapshot** `[5, 10, 18, **20**, **30**, 30, 51, 99, 50, 30, 100, 33]`. The original first sublist `[10, 18, 5, 30, 20]` has been sorted.

Phase 4 - Partition the second sublist. (Snapshots 7-9 are omitted for brevity)

Repeat the process on the second sublist `[51, 99, 50, 30, 100, 33]`. The pivot is the first element 51. `**i**` and `**j**` swap the bigger 99 with the smaller 33, but nothing further. Then the pivot is swapped with the element at position `**j**`, the 30. This partitions `[51, 99, 50, 30, 100, 33]` into sublists `[30, 33, 50]` and `[100, 99]` with the pivot 51 between them.

Phase 5 - Continue partitioning until the second sublist is sorted.

Repeat the process on these two sublists. `[30, 33, 50]` is already sorted. After swapping the 99 with the 100 the second sublist is sorted, and therefore the entire list is also.

The function should return

```
[ [30, 18, 5, 30, 51, 10, 20, 99, 50, 30, 100, 33], 
  [30, 18, 5, 30, 20, 10, 51, 99, 50, 30, 100, 33], 
  [10, 18, 5, 30, 20, 30, 51, 99, 50, 30, 100, 33], 
  [10, 5, 18, 30, 20, 30, 51, 99, 50, 30, 100, 33], 
  [5, 10, 18, 30, 20, 30, 51, 99, 50, 30, 100, 33], 
  [5, 10, 18, 20, 30, 30, 51, 99, 50, 30, 100, 33], 
  [5, 10, 18, 20, 30, 30, 51, 33, 50, 30, 100, 99], 
  [5, 10, 18, 20, 30, 30, 30, 33, 50, 51, 100, 99], 
  [5, 10, 18, 20, 30, 30, 30, 33, 50, 51, 99, 100] ] 
```

NOTE: The list must change between any two snapshots, by swapping the correct two elements as outlined above. There are other implementations of quicksort, but this one is ours!

Quicksort is one of the most widely used sorting algorithms. It is fast (actual implementations obviously don't capture snapshots, so they sort large lists quickly.) It also needs minimal additional storage, because it simply swaps elements. In practice, once the sublists become short, simpler algorithms like Insertion Sort can be used. The library sorting function for many programming languages is a version of Quicksort.

#### Related Kata

This kata was inspired by [Bubblesort Once](https://www.codewars.com/kata/56b97b776ffcea598a0006f2) and [Bubble Sort](https://www.codewars.com/kata/57403b5ad67e87b5e7000d1d).  
Other kata related to quicksort are [Bug Fix - Quick Sort](https://www.codewars.com/kata/56bdaa2cbe8f29257c000085) and [Verified Sorting Algorithms - Quicksort](https://www.codewars.com/kata/5d2f292b496f8d001c224972).
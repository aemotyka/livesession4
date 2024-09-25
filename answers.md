# CMPS 6610  Answers for Lab 04

In this lab, we'll work a bit more with sequences and look at the
divide-and-conquer paradigm.

The sorting algorithms we've discussed so far all work by *comparing* numbers (e.g., `merge_sort`, `insertion_sort`, `selection_sort`). Today, we'll look at an algorithm that sorts without making any pairwise comparisons.

The algorithm is particularly suited for sorting lists with the
following properties:

- elements are non-negative integers

- the maximum element is not too big

- many elements are repeated

For example:

$[2,2,1,0,1,0,1,3] \rightarrow [0,0,1,1,1,2,2,3]$

In addition to the input list of length $n$, the algorithm also takes as input the maximum value in the list ($k$). E.g., $k=3$ in the above example.

The algorithm proceeds by first counting how often each value appears. Based on these counts, the algorithm figures out the range of output locations to place each value. For example, because there are two 0s and three 1s, we know that the first 1 goes in the 3rd position, and the final 1 goes in the 5th position. We'll complete the algorithm `supersort` by implementing three functions, `count_values`, `get_positions`, and `construct_output`.



1. Implement a simple, sequential version of `count_values` (linear span and work) and test it with `test_count_values`.

.  
.  
.  

2. Continuing our example, `counts` should now be `[2, 3, 2, 1]`. We next need to convert this into a list indicating the location of the first appearance of each value in the output.

E.g., `positions=[0, 2, 5, 7]` means that, in the final output, the first 0 appears at index 0, the first 1 appears at index 2, etc.

We can use `scan` to create the needed list. You may need to adjust slightly the output of scan to get the needed list. Complete `get_positions` and test with `test_get_positions`.

.  
.  
. 


3. What is the work and span of `get_positions`? (assume our more efficient version of `scan` from class)

> **put in answers.md**

get_positions calls scan once and then returns part of its output, so its work and span will be those of scan itself. The efficient version of scan we learned in class had both work and span of O(n), so get_positions will also have both work and span of O(n).


4. Finally, we'll use this positions array to construct the final output. First, we'll create the output list ($n$ elements). Then, we will loop through the original input array once again. For each value, we'll look up in the `positions` array where the value should go. E.g., for the first value 2, we look up `positions[2]`, which tells us the 2 should go in index 5 in the output. To update `counts` for future iterations, we will then increment `counts` by one for the value we just read. E.g., `positions[2]` will increment from 5 to 6; the next 2 we read will be placed in index 6.

Implement `construct_output` with a simple for loop and test with `test_construct_output`.

.  
.  
. 


5. What is the work and span of `construct_output`?

> **put in answers.md**

construct_output relies on a few constant time operations, and a for loop that loops over the input once. Thus, it is not parallelizable and the work and span will be the same. Since there are no nested for loops, and the expressions contained in the for loop are all constant time, the work and span of construct_output are O(n).


6. What is the work and span of `supersort`?

> **put in answers.md**

supersort is a composition of count_values, get_positions, and construct_output. Thus, its work and span will be the sum of the work and span of these three operations, which we have already been provided above or have otherwise calculated:

count_values: linear work and span (from question 1)
get_positions: linear work and span (from question 3)
construct_output: linear work and span (from question 5)

Thus:

W_supersort = O(n) + O(n) + O(n) : O(n) work
S_supersort = O(n) + O(n) + O(n) : O(n) span


7. Our implementation of `count_values` has poor span. Let's instead implement it using map-reduce. Complete `count_map`, `count_reduce`, which are used by `count_values_mr` to construct the `counts` variable using map-reduce. Test with `test_count_values_mr`.

.  
.  
. 


8. What is work and span of `count_values_mr`?

> **put in answers.md**
Map has O(n) work and O(1) span, assuming map calls are parallelized.

Collect involves a for loop and sorting- i assume this uses merge sort, which has O(n log n) time complexity.

Reduce has O(n) work and O(log n) span (as solved for in assignment 4), assuming recursive calls are done in parallel.

Iterate is sequential and has O(n) work and span.

So:

Work : O(n) + O(n log n) + O(n) + O(n) = O(n log n) work.
Span : O(1) + O(n log n) + O(log n) + O(n) = O(n log n) span.

9. We'll turn our focus now to the divide-and-conquer paradigm that we
   are formalizing in Module 4. Recurrences naturally model the
   performance of a divide-and-conquer algorithm, and we have had
   plenty of practice with these. Let's focus on proving correctness.
   
   
a) Before we look at proving the correctness of an algorithm, let's
   get some practice using mathematical induction. Recall that the
   formula for a geometric series was, for $\alpha \neq 0$:
   $$ \sum_{i=0}^n \alpha^i = \frac{\alpha^{n+1} - 1}{\alpha - 1} $$
   
   Prove that this formula is correct by induction.

> **put in answers.md**
Proof by induction:
base case:
n = 0
so:
a^0 = 1
and:
(a^0+1 - 1)/(a - 1) = (a - 1)/(a - 1) = 1.
Thus, the base case is true for n = 0.
inductive step:
prove the formula holds for n = n + 1.
This would give us:
the sum from i = 0 to n + 1 of a^1 = (a^n+2 - 1)/(a - 1)
which is equivalent to the sum from i = 0 to n of a^1 + a^n+1
substitute the formula for the sum and we get:
(a^n+1 - 1)/(a - 1) + a^n+1 = (a^n+1 - 1)/(a - 1) + a^n+1(a - 1)/(a - 1) 
= (a^n+1 - 1 + a^n+2 - a^n+1)/(a - 1)
simplifying, we get:
(a^n+2 - 1)/(a - 1)
which is what we wanted to prove.

b) Prove the correctness of `reduce` using induction.
	   

> **put in answers.md**
Proof by induction:
Base case 1:
a is empty, and id_ (the identity element) is returned. This is correct because if a is empty. there are no elements to apply f to.
Base case 2:
a contains one element, and that one element is returned. This is correct because f(id_, a) == a.
Inductive step:
Assume reduce(f, id_, a) is correct for some a of size up to n.
Prove that reduce(f, id_, a) is still correct for a of size n+1:
a can be represented as [a[0], a[1], ... a[n]] (because we need an element at index 0, a[n] is the n+1th element of a).
reduce() divides a into two halves:
[a[0], a[1], ... a[n/2]] and [a[n/2+1], a[n/2+2], ... a[n]]
Since reduce produces the correct result for a of up to size n, and both halves of a are now clearly smaller than this, reduce returns the correct result for the recursive call on both halves of a.
From Canvas/the textbook, we know that f is an associative function. Therefore applying f() to the value returned by reduce on each half of a, will produce the correct result for a of size n + 1.

c) Prove the correctness of the divide and conquer version of `span` using induction. 


> **put in answers.md**
Assuming the implementation of fastscan() from module 3.
Proof by induction:
Base case 1:
a is empty, and the function returns a tuple containing an empty list and then id_. This is correct because the "prefix scan" of an empty list is an empty list, and it's reduction is id_.
Base case 2:
a contains one element, in which case it returns ([id_], a[0]). This is correct because a "prefix scan" of a list of one element will have id_ as its one and only element, and the only element of a will be its reduction.
Inductive step:
Assume scan(f, id_, a) is correct for a of up to size n. 
Prove that scan(f, id_, a) is still correct for a of size n+1:
As in question b, a of size n+1 = [a[0], a[1], ... a[n]].
The function creates subproblems of adjacent pairs of elements in a, and applying f to them.
The function is recursively called on each of these subproblems, which are all of size < n, so we can assume that scan() returns the correct value for all of them. 
We assume that the step combining solutions of subproblems is correct up until the last element, which is the result of subproblem f(a[n-1], a[n]). Since this subproblem is of length < n, it is handled correctly as well. 

d) Prove the correctness of the contraction-based version of `span`
from Module 3. Compare this proof to the divide-and-conquer
version. Are they significantly different? Was one easier than the
other?

> **put in answers.md**
Proof by induction:
Base case:
(from reduce)
a is empty, and scan() returns ([], id_).
This is correct because [] is the prefix scan of [], and id_ is the reduction of [].
Inductive step:
Assume scan(f, id_, a) is correct for a of up to size n, and that reduce(f, id_, a) is correct for all a.
Prove that scan(f, id_, a) is still correct for a of size n+1:
As in question b, a of size n+1 = [a[0], a[1], ... a[n]].
The function first computes the prefix scan of sub-lists of a up to size n, which are all correct because sub-lists of a are smaller than a, and these are assumed to return correctly. 
The last operation simply involves reduce(f, id_, a), which we know to be correct because reduce is assumed to be correct for all a.

The proofs are not very different, as they both essentially involve proving that the inductive step breaks down the problem into subproblems of a size we know the recursive call will return correct for, and showing that recombining these solutions to subproblems is correct as well.

The sequential version of scan() was easier for me to prove, because the for-loop makes it clear that the subproblems are all less than size n, and all operations clearly depend on reduce(), which is assumed to be correct.
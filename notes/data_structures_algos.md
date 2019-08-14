# Data Structures and Algorithms

## Big O and Rates of Growth

| Time Complexity       | Name           | Example  |
| -------------         |:-------------: | -----:|
| 1                     | Constant | Add Element to linked list |
| log(n)                | Logarithmic      |   Search Sorted Array (Binary Search) |
| n | Linear          |    Find Element in Unsorted Array |
| n log(n)              | Linear Log | Sorting n items with mergsort |
| n^2                   | Quadrtic      |   Shortest path |
| n^3                   | Cubic      |    Matrix Multiplication |
| 2^n                   | Exponential | Towers of Hanoi |

## Recursion vs. Iteration

### Recursion
- Requires extra space on the stack frame for each sucessive call.
- Infinite recursion: program could run out of memory and give a stack overflow
- Solutions to problems are easier to formulate recursively than iteratively.

### Iteration
- Iteration does not require extra space.
- Infinite loop could loop forever.
- May not have as obvious of a solution as the recursive version of the problem.

## Linked Lists
- insert/delete
- delete list
- count
- nth node from end of list, from beg of list
- access time O(n) means hard to access

## Arrays
- simple, easy
- fast access of elements
- fixed size, single block alloc
- complex insertion (if in middle of list - shift existing elements.

## Dynamic Arrays
- growable, resize based on elements in array
# Data Structures Algorithms

Study material notes and topics from books for programming interviews.

## Big O and Rates of Growth

| Time Complexity       | Name           | Example  |
| -------------         |:-------------: | -----:|
| 1                     | Constant | Add Element to linked list |
| log(n)                | Logarithmic      |   Search Sorted Array (Binary Search) |
| n | Linear          |    Find Element in Unsorted Array |
| n log(n)              | Linear Log | Sorting n items with merge sort |
| n^2                   | Quadratic      |   Shortest path |
| n^3                   | Cubic      |    Matrix Multiplication |
| 2^n                   | Exponential | Towers of Hanoi |

## Recursion vs. Iteration

### Recursion
- Requires extra space on the stack frame for each successive call.
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

## Programming Notes

## 11 Object Oriented Programming

- encapsulation: hiding of implementation details
- loosely coupled  code, public method provides interface to other classes

- inheritance: allows class to be defined as a modified or special version of another
    - B inherits from A, A is B's parent class, B is A's subclass
- polymorphism: runtime selection of which definition of method should be executed based on class of object.

- interface: declares set of related methods
    - is just declaration of methods, outside of a class
- abstract class: incomplete class definition
    - is a proper class with data members and method definitions

- virtual methods: child class overrides of parent class methods, looked up in vtable for execution
    -pure virtual method: declared but not defined, must be defined in a subclass for execution.

- multiple inheritance
    - ambiguity, disallowed in C#

** Review for OOP **
    - C++: classes, methods, etc.
    - Python: classes, data members, modules, methods, polymorphism

## 12 Design Patterns:
    - Games/Gang of 4 Book
        - Dirty/Flag vs Observer
    - 3 Types:
        creational, behavioral, structural
### Common Design Patterns
#### Singleton
    - ensures at most one instance of a class exists at any given time.
    - gatekeeper to shared resources or central communication
    - all methods accessed through singleton instance of class
    - can inherit from base classes and implement interfaces
    - possible multiplicity later on
    - dynamic binding: class for actual singleton determined at runtime
        - switch between different singleton instance types at runtime
##### Lazy Loading
    - initialize singleton class first only when required instead of on application load.
    - Find C++ singleton initialization code

#### Builder
    - use class to build another class, hiding constructor parameters from original class in case it changes.
    - use builders when objects are complex to construct or take many steps, makes it easier to construct those objects and abstract out difficult or specific steps

#### Iterator
    - traverse elements in data structure without knowing how its stored/represented, and return elements.
    - bidirectional or unidirectional traversal, and add/remove elements at iterator place.
#### Observer
    - broadcast changes in state to observers without needing direct access to observers and underlying objects.
    - Publish/Subscribe pattern:
    - Model View Controller: changes to model automatically issue view/UI changes or redraw calls
#### Decorator
    - wrapper: modify behavior of object without inheritance
    - component systems:
    - Component, Concrete Component, Decorator, Concrete Decorator
        - (Look this up more because it seems a little confusing)
##### Decorator vs. Inheritance


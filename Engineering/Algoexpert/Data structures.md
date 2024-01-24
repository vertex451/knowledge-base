# Complexity
- Usually it describes worst-case scenario
- It takes into an account the most expensive part of an algorithm. O(n) + O(n^2) => O(n^2)
![[complexity_chart.png]]

## Logarithm
- `log(n) = x` if and only if `2^x = n`
- In terms of complexity the base is 2.
- Logarithm answers a question to which number we should power 2 to get n.
- Algorithm has logarithmic complexity when doubling the input increases the number of needed operations by one:
```java
2^x = n
2^3 = 8  // input is 8, number of operations is 3
2^4 = 16 // input is 16, number of operations is 4
2^5 = 32 // input is 32, number of operations is 5
2^10 = 1000
```
- Think about number of operations as how much time we should split the input to get the result (Binary search)
# Memory
- `Bit` - short for binary digit - is a fundamental unit of information in Computer Science that represents a state with one of two values, typically 0 and 1. Any data stored in a computer is, at the most basic level, represented in bits.
- `Byte` - a group of 8 bits. A single byte can represent up to 256 values (2^8).
```java
1: 00000001 (0000000 + 2^0)
7: 00000111 (00000  + 2^2 + 2^1 + 2^0)
255: 11111111 
```
- To store bigger numbers in memory the system uses several bytes in a row. But you should instruct the system that this number should be int32 or int64 for instance.
- 1 in 32-bits(8x4) integer format:
```java
00000000 00000000 00000000 00000001
```
- 1 in 64-bits(8x8) integer format:
```java
00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000001
```
# Array
Array is a linear collection of data values that are accessible at numbered indices, starting at index 0.

Example of storing the array of `[]int8{1, 2, 3}` in memory:
![[memory_array.png]]
*Note: If we deal with `[]int32{}` array, each block would contain not 8 but 32 bits.* 
So, since the system know how many bits is reserved for each entry of array, it can do the simple calculation to access element by index.  For instance, if we want to access `arr[1]`, having the array of  `[]int8{}`, system will take the starting memory slot(3) and increment it by `bit width * index`. That will be starting point. And to get number the system should read 8 bits
```java
starting point:  3 + 8 * 1 = 11
Read bits: 11, 12, 13, 14, 15, 16, 17, 18.
Value: 00000010
```
Complexity:
- **Accessing a value at a given index**: O(1)
- **Updating a value at a given index**: O(1)
- **Inserting a value: O(n). At dynamic arrays if there is capacity: O(1).
- **Removing a value at the beginning**: O(n)
- **Removing a value in the middle**: O(n)
- **Removing a value at the end**: O(1)
- **Copying the array**: O(n)
#  Linked List(LL)
## Singly LL
A data structure that consists of nodes, each with some value and a pointer to the next (and prev) node in the linked list. 
Has `head` and `tail`.
```
0 -> 1 -> 2 -> 3 -> 4 -> 5 -> null
```
Complexity
- **Accessing the head**: O(1)
- **Accessing the tail**: O(n)
- **Accessing a middle node**: O(n)
- **Inserting / Removing the head**: O(1)
- **Inserting / Removing the tail in a singly LL**: O(n) (we need to traverse there)
- **Searching for a value**: O(n)
## Doubly LL
```
null <- 0 <-> 1 <-> 2 <-> 3 <-> 4 <-> 5 -> null
```
Difference in complexity
- **Accessing the tail**: O(1)
- **Inserting / Removing the tail**: O(1)
## Circular LL
A linked list that has no clear **head** or **tail**, because its "tail" points to its "head," effectively forming a closed circle.
A circular linked list can be either a **singly circular linked list** or a **doubly circular linked list**.
# Hash table
A data structure that provides fast insertion, deletion, and lookup of key/value pairs.

Under the hood, a hash table uses an array of **linked lists** to efficiently store key/value pairs. 
When inserting a key/value pair, a hash function derives from the key an integer value, which must be LE than array length.(It uses modulus division by array length) and stores it in the linked list which lives behind the index.
In LL both original key and value are stored. We need this for further lookup by traversing the LL.

Below is an example of what a hash table might look like under the hood:

```java
[ 
0: (value1, key1) -> null 
1: (value2, key2) -> (value3, key3) -> (value4, key4) 
2: (value5, key5) -> null 
3: (value6, key6) -> null 
4: null 
5: (value7, key7) -> (value8, key8) 
6: (value9, key9) -> null 
]
```
## Complexity
- **Inserting a key/value pair**: O(1) on average; O(n) in the worst case(if all keys are stored behind the same index, you need to traverse the whole list to double check if the key is already present before insertion)
- **Removing a key/value pair**: O(1) on average; O(n) in the worst case
- **Looking up a key**: O(1) on average; O(n) in the worst case

The worst-case linear-time operations occur when a hash table experiences a lot of collisions, leading to long linked lists internally, which take O(n) time to traverse.

However, in practice and especially in coding interviews, we typically assume that the hash functions employed by hash tables are so optimized that collisions are extremely rare and constant-time operations are all but guaranteed.
## Resizing
The primary reasons for resizing are to prevent excessive collisions and to ensure that the Load Factor remains within an acceptable range.
```java
Load Factor = Key-Val pairs / array slots.
```
A common threshold value for the load factor is 0.7
Resizing flow:
1. Memory allocation for new array(typically x2)
2. Rehashing
3. Insertion into the New Array
4. Deallocation of the Old Array
# Stack and Queue
## Stack
A stack is an array-like data structure whose elements follow the **LIFO** rule: **L**ast **I**n, **F**irst **O**ut.
A stack is typically implemented with a **dynamic array** or with a **singly linked list**.
Think of a stack of books on a table.
### Complexity
- **Pushing an element onto the stack**: O(1)
- **Popping an element off the stack**: O(1)
- **Peeking**(Retrieves the element from the top of the stack without removing it): O(1)
- **Searching for an element in the stack**: O(n)
## Queue
A queue is an array-like data structure whose elements follow the **FIFO** rule: **F**irst **I**n, **F**irst **O**ut. 
A queue is typically implemented with a **doubly linked list**.
### Complexity
- **Enqueuing an element into the queue**: O(1)
- **Dequeuing an element out of the queue**: O(1)
- **Peeking at the element at the front of the queue**: O(1)
- **Searching for an element in the queue**: O(n)
# String
String is a data type used to represent text or a sequence of characters. It is stored in memory as arrays of integers, where each String's character is mapped to an integer using character-encoding standards like ASCII.

in most programming languages, strings are **immutable**, meaning that they cannot be edited after creation. 
Appending a character to a string or even altering are more expensive than they might appear.
```python
string = "this is a string"
newString = ""
for character in string:
    newString += character
```
The operation above has a time complexity of **O(n^2)** - each addition of a character to `newString` creates an entirely new string and is itself an **O(n)** operation. 
Therefore, `n * O(n)` operations are performed.
# Graph
A collection of nodes or values called **vertices** that might be related; relations between vertices are called **edges**. 
## Cyclic Graph
A cycle occurs in a **graph** when three or more **vertices** in the graph are connected so as to form a closed loop. 
Number can be different, clarify at coding interviews, what exactly constitutes a cycle.
## Acyclic Graph
A **graph** that has no **cycles**.
## Directed Graph
A **graph** whose **edges** are directed, meaning that they can only be traversed in one direction, which is specified. 
Example: Airport graph.
## Undirected Graph
A **graph** whose **edges**  can be traversed in both directions. 
Example: Facebook friendships.
## Connected Graph
A **graph** is connected if for every pair of **vertices** in the graph, there's a path of one or more **edges** connecting the given vertices. 
In the case of a **directed graph**, the graph is:
- **strongly connected** if there are bidirectional connections between the vertices of every pair of vertices (i.e., for every vertex-pair `(u, v)` you can reach `v` from `u` and `u` from `v`).
- **weakly connected** if there are connections (but not necessarily bidirectional ones) between the vertices of every pair of vertices.
# Tree
A tree is effectively a **graph** that's **connected**, **directed**, and **acyclic**, that has an explicit root node, and whose nodes all have a single **parent** (except for the root node, which effectively has no parent)
- Has a **root node**, and **leaf nodes** or simply **leaves**.
- The paths between the root of a tree and its leaves are called **branches**, and the **height** of a tree is the length of its longest branch. 
- The **depth** is its distance from its tree's root; this is also known as the node's **level** in the tree.
## Binary Tree
A **tree** whose nodes have up to **two** child-nodes.
The structure of a binary tree is such that many of its operations have a logarithmic time complexity, making the binary tree an incredibly attractive and commonly used data structure.
## K-ary Tree
A **tree** whose nodes have up to **k** child-nodes. 
### Perfect Binary Tree
A **binary tree** whose interior nodes all have two child-nodes and whose **leaf nodes** all have the same **depth**. Example:
```
           1
      /         \
     2           3
   /   \       /   \
  4     5     6     7
 / \   / \   / \    / \
8   9 10 11 12 13  14 15
```

## Complete Binary Tree
A **binary tree** which **leaf nodes** don't necessarily all have the same **depth**. Child nodes should be populated from left to right
```
	       1
	    /     \
	   2       3
	 /   \   /   \
	4     5 6     7
   / \
  8   9
```

Conversely, the following binary tree *isn't* complete, because the nodes in its last level aren't as far left as possible:
```
	       1
	    /     \
	   2       3
	 /   \   /   \
	4     5 6     7
		         /  \
		        8    9
```
## Balanced Binary Tree
A **binary tree** whose nodes all have left and right **subtrees** whose **heights** differ by no more than 1.
```
	       1
	    /     \
	   2       3
	 /   \   /   \
	4     5 6     7
   / \           /   
  10  9         8
```
A balanced binary tree has the logarithmic time complexity of its operations.
For example, inserting a node at the bottom of the *imbalanced* binary tree would clearly not be a logarithmic-time operation, since it may involve traversing through most of the tree's nodes.
## Full Binary Tree
A **binary tree** whose nodes all have either two child-nodes or zero child-nodes. Example:
```
   1
 /   \
2     3
    /   \
   6     7
 /   \
8     9
```


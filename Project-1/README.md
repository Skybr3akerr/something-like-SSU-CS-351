## Compiler Optimization Results

| OPT     | NUM_BLOCKS | MIN_BYTES | MAX_BYTES | alloca.out avg | list.out avg | malloc.out avg | new.out avg |
|---------|------------|-----------|-----------|----------------|--------------|----------------|-------------|
| -g      | 100000     | 10        | 10        | 0.032000       | 0.093000     | 0.023000       | 0.076000    |
| -O2 -g2 | 100000     | 10        | 10        | 0.010000       | 0.021000     | 0.018000       | 0.019000    |


## Data Per Node Results

| OPT     | NUM_BLOCKS | MIN_BYTES | MAX_BYTES | alloca.out avg | list.out avg | malloc.out avg | new.out avg |
|---------|------------|-----------|-----------|----------------|--------------|----------------|-------------|
| -O2 -g2 | 100000     | 10        | 10        | 0.010000       | 0.019000     | 0.019000       | 0.020000    |
| -O2 -g2 | 100000     | 100       | 100       | 0.041000       | 0.057000     | 0.053000       | 0.055000    |
| -O2 -g2 | 100000     | 1000      | 1000      | 0.396000       | 0.400000     | 0.383000       | 0.383000    |
| -O2 -g2 | 100000     | 4000      | 4000      | 1.484000       | 1.437000     | 1.418000       | 1.421000    |


## Block Chain Length Results

| OPT     | NUM_BLOCKS | MIN_BYTES | MAX_BYTES | alloca.out avg | list.out avg | malloc.out avg | new.out avg |
|---------|------------|-----------|-----------|----------------|--------------|----------------|-------------|
| -O2 -g2 | 10000      | 100       | 100       | 0.006000       | 0.010000     | 0.010000       | 0.008000    |
| -O2 -g2 | 100000     | 100       | 100       | 0.048000       | 0.050000     | 0.051000       | 0.051000    |
| -O2 -g2 | 1000000    | 100       | 100       | 0.438000       | 0.538000     | 0.468000       | 0.526000    |
| -O2 -g2 | 10000000   | 100       | 100       | 4.279000       | 5.380000     | 4.514000       | 5.129000    |


## Heap Break Results

| OPT     | NUM_BLOCKS | MIN_BYTES | MAX_BYTES | alloca.out breaks | list.out breaks | malloc.out breaks | new.out breaks |
|---------|------------|-----------|-----------|-------------------|-----------------|-------------------|----------------|
| -O2 -g2 | 10000      | 100       | 100       | 69 (nice)         | 85              | 83                | 85             |
| -O2 -g2 | 100000     | 100       | 100       | 69 (very nice)    | 227             | 209               | 227            |
| -O2 -g2 | 1000000    | 100       | 100       | 69 (most nicest)  | 1643            | 1468              | 1643           |


1. Which program is fastest? Is it always the fastest?
The fastest one is alloca, generally.
There are two instances where malloc overtakes it by a small margin (in the first compiler optimization result, and in the third data per node result),
but other than that, it's pretty dominant.


2. Which program is slowest? Is it always the slowest?
The slowest is pretty close between list and new, but list is marginally worse in most cases.
There are some cases where it's tied (all heap break experiments), and one case where new is worse (the first data per node test).
Besides that, list is worse.


3. Was there a trend in program execution time based on the size of data in each Node? If so, what, and why?\
Yeah, it's kind of expected. As data size per node increased, execution time increased.
That's because each program has to initialize and hash more bytes per node as data size goes up.


4. Was there a trend in program execution time based on the length of the block chain?
Again, yes, and it's kind of a given. Length increased, and so did runtime. No way! That's crazy!
More nodes equals more allocations, more pointer linking, more general work. Who would have thought!


5. Consider heap breaks, what's noticeable? Does increasing the stack size affect the heap? Speculate on any similarities and differences in programs.
The heap-based programs had heap break behavior because they allocate memory from the heap.
The alloca one behaved differently because it allocates memory on the stack.
Increasing the stack size let alloca run with larger lists, but it did not mean that the heap itself increased.

6. Considering either the malloc.cpp or alloca.cpp versions of the program, generate a diagram showing two Nodes. Include the diagram
	the relationship of the head, tail, and Node next pointers.
	show the size (in bytes) and structure of a Node that allocated six bytes of data
	include the bytes pointer, and indicate using an arrow which byte in the allcoated memory it points to.

head
 |
 v
+------------------+        +------------------+
| Node 1           |        | Node 2           |
| next ------------+------> | next ------------+----> nullptr
| numBytes = 6     |        | numBytes = 6     |
| bytes -----------+--+     | bytes -----------+--+
+------------------+  |     +------------------+  |
                      v                           v
                  +---+---+---+---+---+---+   +---+---+---+---+---+---+
                  | b0| b1| b2| b3| b4| b5|   | b0| b1| b2| b3| b4| b5|
                  +---+---+---+---+---+---+   +---+---+---+---+---+---+

tail --------------------------------------------------------^


7. There's an overhead to allocating memory, initializing it, and eventually processing (in our case, hashing it).
   For each program, were any of these tasks the same? Which one(s) were different?
All programs generated node data, built a list, traversed the list, and computed the same result.
The difference lied in the strategy.
`list.cpp` used standard C++ containers, `new.cpp` used manual linked-list allocation with `new`,
`malloc.cpp` used `malloc()` plus placement `new`, and `alloca.cpp` used stack allocation with `alloca()`.


8. As the size of data in a Node increases, does the significance of allocating the node increase or decrease?
As the size of data in each node increased, the (relative) significance of allocating the node appeared to decrease.
That's because more total time was used initializing and hashing the bytes, as that operation took more time than allocation.
Obviously with smaller nodes, allocation overhead was a larger player.
# Performance-Engineering
My Collection of notes on performance

Performance is like currency, that gives you the ability to buy more features tomorrow.

## Is Performance Good?

### Profiling
1. Look deeply into the program execution.
2. Find out where you are spending your time
    2.1 By method
    2.2 By line
3. Lots of interesting information
    3.1 Time spend
    3.2 Cumulative time spend
    3.3 Number of invocations
    etc etc
4. Great way to zero in on what matters
    Hotspots
    4.1 If 90% time is in one routine, inefficiencies in the rest of the program don't matter.
    4.2 Also, is the Hotspots doing what you expect them to do?

### Issues with Immutability

When you work with immutable objects, you are basically going to copy the entire data structure, make a new pointer for the updated object. So even though your algorithm seems harmless or having less time complexity, might actually be of higher complexity.

Copying is costly
  * Cost of making duplicates
  * Cost of garbage collecting the freed objects
  * Huge memory footprint

### Issues with Dynamic Dispatch

Method call overhead
  * Multiple subtypes: What method to call depends on the object
  * Each method call needs to loop-up the object type in a dispatch table.
  * Dynamic dispatch is an address is an address lookup + indirect branch.

Indirect branches are costly
  * Modern microphones are deeply pipelined
    * 12 pipelines in core 2 duo, 20 in Pentium 4
    * ie hundreds of instructions in flight
  * Need to be able to keep fetching next instructions before executing them.
  * Normal instructions: keep fetching the next instructions
  * Direct branch: target address known can fetch ahead from target
    * Works for conditional branches by predicting the branch
  * Indirect branch: target unknown, need to wait until address fetch completes

### Issues with object oriented
Memory fragmentation
  * objects are allocated independently
  * All over memory
  * If contiguos in memory -> getting to the next item is just a index increment

So in some cases we can improve the performance drastically by removing object style and directly using structures like arrays.

### Moving from Java to C
Whenever we move from a higher level language to a lower one, there will be some performance gains.
lets see diff between:
* Java
 Memory bounds check
 Bytecode first interpreted and then JITed(fast compilation, no time to generate the best code)

* C
 No Memory bounds check
 Intel C Compiler compiles the program directly into x86 assembly.

### Profiling with performance counters
Modern hardware counts "events" - lot more information than just execution time.
CPI- Clock cycles per instruction -> measures if instructions are stalling.
L1 and L2 cache miss rate -> are your access using the cache well or is the cache misbehaving?
Instructions Retired -> How many instructions got executed.

### General notes
Contiguous accesses are better -> data fetch as cache line, Contiguous data -> single cache fetch supports 8 reads of doubles.

check for possibilities to preprocess data to reduce the complexity.

### cache hierarchy
Store the most probable accesses in small amount of memory with fast access.
Hardware heuristics determine what will be in each cache and when.

### The temperamental cache
If your access pattern matches heuristics of the hardware, then you program will be blazingly fast.

### Changing the program
Many ways to get to the same result
* change the execution order
* change the algorithm
* change the data structures

### Some changes can perturb results
* Select a different but equivalent answer.
* Reorder arithmetic operations
  (a+b)+c is not the same as a+(b+c)
* Drop/change precision
* Operate within acceptable error range.

### Instruction level optimizations
Modern processors have many other performance tricks
* Instruction level parallelism
2 integers, 2 floating point and 1 MMX/SSE
* MMX/SSE instructions
  can do the same operation on multiple contiguous data at the same time.
* cache hierarchy
* prefetching of data

### Nudge the compiler
need to nudge the compiler to generate the vector code
* remove any perceived dependencies
* Bound most constant variables to the constant

### Tuned libraries
BLAS Libraries
* Hand tuned library in C/assembly to take full advantage of hardware.
* see http://www.netlib.org/blas & http://ressim.berlios.de

The BLAS (Basic Linear Algebra Subprograms) are routines that provide standard building blocks for performing basic vector and matrix operations. The Level 1 BLAS perform scalar, vector and vector-vector operations, the Level 2 BLAS perform matrix-vector operations, and the Level 3 BLAS perform matrix-matrix operations. Because the BLAS are efficient, portable, and widely available, they are commonly used in the development of high quality linear algebra software, LAPACK for example.

### Intel Math kernel library
specially tuned library for x86

### Parallel Execution
Multicores are here
* 2 to 6 core in a processor
* 1 to 4 processor in a box
* cloud machines have 2 processors with 6 cores(total 12 cores)

### Use concurrency for parallel execution
* Divide the computation into multiple independent/concurrent computations.
* Run the computations in parallel
* Synchronize at the end

### Issues with parallelism
#### Amdhal's Law
* Any computation can be analyzed in terms of a portion that must be executed sequentially, Ts and a portion that can be executed in parallel, Tp. Then for n processors:
  * T(n) = Ts + Tp/n
  * T(infinity) = Ts, thus maximum speedup (Ts + Tp)/Ts

#### Load Balancing
The work is distributed among processors so that all processors are kept busy all of the time.

#### Granularity
The size of the parallel regions between synchronizations or the ratio of computations(useful work) to communication(overhead).

### Swaps
Swap two integers x and y
Solution 1 : t = x;
x = y;
y = t;
Solution 2: Swap two integer without a temporary variable
x = x ^ y;
y = x ^ y;
x = x ^ y;

Why it works?
XOR (^) is its own inverse: (x^y)^y = x

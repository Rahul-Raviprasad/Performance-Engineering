# Performance-Engineering
My Collection of notes on performance


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

### Issues  

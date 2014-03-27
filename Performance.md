## Measuring performance
========================

Performance should be judged by several different metrics for different computers (e.g personal computer, server)

1. Throughput
    - For server: how many jobs get done in a certain amount of time? 
    - things-per-second
    - the _larger_ the better

2. Execution time
    - how much time needed to complete one job?
    - for personal computers, embedded systems
    - Execution time = 1 / Throughput
    - the _smaller_ the better

We primarily focus on **execution** **time**, i.e. response time:

> Computer A is _3_ times faster than computer B.

That means

    Execution time of B/ Execution time of A = 3

However the execution time varies for one task if it is running on different CPU...

### CPU Performance
***

    Elapsed time = CPU time + Access time + Overhead

CPU time: the time the processor spends computing that particular application

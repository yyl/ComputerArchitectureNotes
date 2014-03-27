## Measuring performance

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

Elapsed time: all the time needed to perform one application while CPU is multiplexing multiple tasks

    Elapsed time = CPU time + Access time + Overhead

CPU time: the time the processor spends computing that particular application

- System performance: performance measure based on _elapsed_ _time_
- CPU performance: performance measure based on _CPU_ _time_

#### Clock cycles

It is easier to measure performance based on how fast computer executes basic/atomic instructions/functions.

    CPU Time = # of clock cycle for a program x clock period
             = # of clock cycle for a program / clock frequency

The _clock_ _period_ is the time interval of a clock cycle for one type of hardware. To relate _clock_ _cycle_ to the instructions of a program:

    # of clock cycle = # of instructions per program x CPI

CPI stands for average clock cycles needed per instruction. Given a program could contain different instructions, which quire different amount of clock cycles to execute, CPI is the average of that amount of all different instructios in a program. 

    CPI = sum(CPI_i x F_i)

`CPI_i` is the # of cycles needed for instruction _i_. `F_i` is the frequency of instruction _i_ in the program, which is defined as:

    F_i = # of instruction i / instruction count per program

Now the `CPU time` could be rewritten:

    CPU Time = # of instructions per program x CPI x clock period

**Big picutre**: the performance of CPU of a particular program now depend on the combination of 3 different measurements. Optimization of only 1 measurement might not lead to improvement over performance as it most likely would decrease another measurement. Trade-off/balance among 3 measurements would be important.



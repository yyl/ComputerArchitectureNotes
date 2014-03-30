Memory
=====

Hierarchical memory design that takes advantage of _principle_ _of_ _locality_ in programs:

- Temporal locality: a data location is referenced before tends to be referenced again soon. Example: loops.
- Spatial locality: a datalocation is referenced, nearby address will tend to be referenced soon. Example: sequential program.

Multiple levels of memory:

- Cache: fastest, smallest, SRAM
- Main memory: DRAM
- Disk: magnetic disk, slowest

As for the performance of a memory implementation, we focus on **hit** **rate** and **miss** **penalty**.

- hit time: time to access upper lvel of memory and time to determine whether the access is a hit or miss
- miss penalty: time to replace a block in upper level with one from lower level, and time to deliver it to the processor

### Caches



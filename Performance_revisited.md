[revisted] Measuring performance
==

### CPU only

> Execution time is the only valid and unimpeachable measure of performance.

    CPU Time = execution time = # of clock cycles x clock cycle period

- the value varies for different program running on different CPUs
- `# of clock cycles = sum(CPI_i x IC_i)`
    - IC_i: Instruction Count of instruction _i_ in given program
    - CPI_i: Clock-cycle needed Per Instruction _i_ with given CPU
- clock cycle period = 1 / clock rate
- note

    Performance = 1 / CPU Time

Alternatively we have Million Instruction Per Second (MIPS),
    
    MIPS = instruction count / (10^6 x execution time)

Faster machines have higher `MIPS`, however:
- it does not take into account capabilities of different instructions. Computer with different instruction sets cannot be compared.
- it varies for different programs on the same computer
- it is not consistent with the `CPU Time` measurement. Program with faster execution time could have lower `MIPS`!

### With miss penalty

    CPU Time = (CPU execution cycles + # of Memory-stall clock cycles) x clock cycle period
    # of memory-stall clock cycles = IC x miss rate x miss penalty

- normally we combine read and write miss into one case by ignoring _write_ _buffer_ _stalls_
- miss penalty is the extra clock cycles CPU has to go through due to a miss

### With multilevel caches

    CPU Time = Total CPI x IC x clock cycle period
    Total CPI = base CPI + primary-stall CPI + secondary-stall CPI
    stall CPI = miss penalty x miss rate
    miss penalty = memory/cache access time x clock rate

- Total CPI: expected CPI including the possibility of miss
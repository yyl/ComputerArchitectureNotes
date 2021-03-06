[revisted] Measuring performance
==

### CPU only

> Execution time is the only valid and unimpeachable measure of performance.

    CPU Time = execution time = IC x CPI x clock cycle period

- the value varies for different program running on different CPUs
- IC: Instruction Count
- CPI: Cycle Per Instruction
    - actually is average CPI of different instructions
    - `sum(CPI_i x IC_i) / IC`
        - `CPI_i`: CPI of instruction i
        - `IC_i`: $ of instructions i in the program
- clock cycle period = 1 / clock rate
- note `Performance = 1 / CPU Time`

If given CPI for different instruction classes, we have

    CPU Time = execution time = sum(CPI_i x IC_i) x clock cycle period

- `CPI_i`: average CPI for instruction class i
- `IC_i`: # of instruction of class i in the program

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

    AMAT = L1 hit time + L1 miss rate x (L2 hit time + L2 miss rate x Memory access time)

- AMAT: Average Memory Access Time
- when L1 miss, we access L2; when L2 miss, we access memory
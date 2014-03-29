Pipelining
========

> Parallelism

Pipelining is an _implementation_ technique to alow multiple instructions overlapped (i.e. executed at the same time). It makes processor fast by increasing its throughput. Note the actual time to complete single instruction does not change.

We splits MIPS instructions into 5 stages. Theoretically a full pipe could make the processor _five_ times faster. However in reality it is just closer to five when pipe gets longer. 

Hazards in pipelining include:

1. Structural hazards: the hardware cannot support the combinations of instructions we want in the same cycle
    - can a single unit be shared in the same cycle?
    - MIPS has two memories?
2. Data hazards: one step must wait for another to complete (wait for data mostly)
    - resolved by _forwarding_: forward the data needed directly to next instruction without putting it into reg
    - graphical representation of different MIPS instructions
3. Control hazards: make a decision based on the results of one instruction while others are executing
    - mostly for branching
    - solution one: stall - too slow
    - solution two: branch prediction
        - always predict to be one branch, or
        - _dynamic_ prediction: based on the past history
    - solution three: delay decision
        - execute some other instructions (say next sequetional instruction)
        - then execute branch one
        - MIPS acutally use this

### Representation of pipeline

**Five-stage** of an instruction

1. IF: Instruction Fetch
2. ID: Instruction decode and register file read
3. EX: Execution or address calculation
4. MEM: Data memory access
5. WB: Write back

#### Pipeline diagram

![Pipeline diagram](images/pipeline_diagram.png)

- each stage is represented by its physical resources used
    - IM: **IF** from instruction machine
    - Reg: **ID** reads from register
    - ALU: **EX** using Algebraic logic unit
    - DM: **MEM** read/write to data memory
    - Reg: **WB** write data back to register
- each stage, _write_ in 1st half, _read_ in 2nd half
    - this enables read and write in the same cycle
- between each pair of two adjcent stages there is a pipeline register
    - any information in later stage must be passed to that stage via a pipeline register
- no write signal for either PC or any pipeline register
    - they are written on each clock cycle

diagram of `lw $1, 100($0)`,

![load word diagram](images/lw_diagram.png)

1. **read** instruction from meory using `$pc`, place it in IF/ID register
2. decode the instruction, get register numbers, **read** two registers, pass results and a extended 32-bit immediate to ID/EX register
3. adds immediate and value of register `$0`, put the sum into EX/MEM register
4. **read** from memory using data from EX/MEM register, then place data into MEM/WB register
5. **write** the data to register (`$1`)

diagram of `sw $15, 100($2)`,

![Store word diagram](images/sw_diagram.png)

The 1st and 2nd steps of all instructions are the same: **read** instruction, decode and **read** register files

3. calculating memory address and put it to EX/MEM register
4. **write** data to target memory address
5. NOTHING happens here.

diagram for `add`, `sub`, `or`, `and` are similar,

![Add diagram](images/add_diagram.png)

- no memory access
- in 5th stage, **write** the result into target register

Pipelining
========

> Parallelism

Pipelining is an _implementation_ technique to alow multiple instructions overlapped (i.e. executed at the same time). It makes processor fast by increasing its **throughput**. Note the actual time to complete single instruction does not change.

We splits MIPS instructions into 5 stages. Theoretically a full pipe could make the processor _five_ times faster. However in reality it is just closer to five when pipe gets longer. 

Hazards in pipelining include:

1. Structural hazards: the hardware cannot support the combinations of instructions we want in the same cycle
2. Data hazards: one step must wait for another to complete (wait for data mostly)
    - resolved by _forwarding_: forward the data needed directly to next instruction without putting it into reg
3. Control hazards: make a decision based on the results of one instruction while others are executing
    - mostly for branching
    - solution 1: stall - too slow
    - solution 2: branch prediction
        - always predict to be one branch, or
        - _dynamic_ prediction: based on the past history
    - solution 3: delay decision
        - execute some other instructions (say next sequetional instruction) to fill the gap
        - then execute branch one
        - MIPS acutally use this
        - good for 5-stage instruction set

### Representation of pipeline

***

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

1. Same as `lw`: **read** instruction
2. Same as `lw`: decode and **read** register files
3. calculating memory address and put it to EX/MEM register
4. **write** data to target memory address
5. NOTHING happens here.

diagram for `add`, `sub`, `or`, `and` are similar,

![Add diagram](images/add_diagram.png)

- no memory access
- in 5th stage, **write** the result into target register

### Data hazards and Forwarding

***

An example,

![Data hazard example](images/data_hazard.png)

- last 4 instructions all depend on the result in `$2`, which is calculated by the 1st instruction.
- but only 2nd and 3rd instructions create data hazard

#### detection of a data hazard

In the graph above, the 1st hazard occurs when `sub` is in MEM stage and following `and` is in EX stage at register `$2`:

    EX/MEM.RegisterRd = ID/EX.RegisterRs = $2

This is data hazard as the next instruction is trying to access the register `$s2` which the current instruction has done writing with. 

A list of similar rules:
    
    EX/MEM.RegisterRd = ID/EX.RegisterRs 
    EX/MEM.RegisterRd = ID/EX.RegisterRt
    MEM/WB.RegisterRd = ID/EX.RegisterRs 
    MEM/WB.RegisterRd = ID/EX.RegisterRt

And corresponding hazard detection conditions for first two (EX hazard):

    if (EX/MEM.RegWrite
    and (EX/MEM.RegisterRd != 0)
    and (EX/MEM.RegisterRd = ID/EX.RegisterRs)) ForwardA = 10

    if (EX/MEM.RegWrite
    and (EX/MEM.RegisterRd != 0)
    and (EX/MEM.RegisterRd = ID/EX.RegisterRt)) ForwardB = 10

- `RegWrite` has to be asserted as it only happen when writing registers
- do not forward possible nonzero of `$0`
    - MIPS does not alllow nonzero values appear in operand register
    - both `ID/EX.RegisterRt` and `ID/EX.RegisterRs` are operand register
- trying to read (as source operand) the register which has done been written with
- `ForwardA` and `ForwardB` are control bits for 2 MUX of forwarding. `01` forwards values from EX/MEM.

MEM hazards, 

    if (MEM/WB.RegWrite
    and (MEM/WB.RegisterRd != 0)
    and (EX/MEM.RegisterRd != ID/EX.RegisterRs)
    and (MEM/WB.RegisterRd = ID/EX.RegisterRs)) ForwardA = 01

    if (MEM/WB.RegWrite
    and (MEM/WB.RegisterRd != 0)
    and (EX/MEM.RegisterRd != ID/EX.RegisterRt)
    and (MEM/WB.RegisterRd = ID/EX.RegisterRt)) ForwardB = 01

- `RegWrite` should be active still
- `$0` cannot be forwarded still
- check if current destination register holds the latest value by comprating with same register but as source operand
- occur when trying to read as operand, while latest result has been computed but has not been written yet
- an example would be summing a vector of numbers into a single register
- `01` forwards values from MEM/WB

### Data hazards and stall

***

Occurs when one instruction tries to read a register after a load instruction writes the same register. The read instruction has to wait.

    if (ID/EX.MemRead and
        (
            (ID/EX.RegisterRt = IF/ID.RegisterRs) or
            (ID/EX.RegisterRt = IF/ID.RegisterRt)
        )
       )
        stall the pipeline

- `MemRead` should be asserted since only `lw` will read the memory
- 2nd and 3rd lines check if the destination EX tries to write to is the target the next instruction tries to read from
- stall is impelemented by `nop` signal that deasserting all 9 control signals
    - no written if all `0`
    - a MUX added after the control unit to select the normal control bit or all `0`s

### Branch hazards

***

The decision of a branch instruction like `beq` would not occur until its MEM stage, forcing the next instruction to wait until then. There are 3 solutions.

#### Always assume branch not taken

- assume not taken, continue sequentially in instructions
- if wrong, 
    - zero all control bits (like a _stall_): discard the instruction
    - flush instructions in IF, ID and EX stages
- then take the branched instruction
    - how?
    - reduce the cost of retaking a branch
        - move branch decision up from MEM
            - move address calculation from EX to ID as we already have `$pc` and immediate fields for calculationg target address
            - move branch decision
        - reduce cost to only 1 instruction (was 2)
- if the probability of untaken is 0.5, we reduce half of the cost of control hazard.

#### Dynamic prediction

Have a history record of the decision on each branch, predict the current one based on history.

- deeper piplines increase branch penlty
- a branch prediction buffer
    - a small memory
    - indexed by lower portion of the address of the branch instruction
    - have 1 or more bits indicating branch taking history
- 1-bit prediction scheme
    - store whether branch is taken last time
    - we assume it is the same as the last one
    - example: a loop that branches 9 times in a row, and then untaken for once
        - the first and last would definitely be wrong
        - 80% accuracy for a branch taken 90% of the time
- 2-bit prediction scheme
    - change the buffer only if predict wrong twice in a row

#### Branch delay

Place an instruction that always executes after the branch to fill the gap waiting for branch decision.

- branch execution will be delayed by one instruction
- effective in 5-stage, but in longer pipelines more instructions needed to fill the gap, which is insufficient
- choose the instruction from
    - code before branch
        - best
        - has to be possible
    - the branch target
        - has to be a high-probability branch
    - fall through

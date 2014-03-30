Questions
=====

### Instructions

***

Q 

Assume variable `h` in `$s2` and the base address of array `A` is in `$s3`. What is the MIPS assembly code for the C assignment statement below?

    A[12] = h + A[8];

ANS

    lw $t1, 32($s3)
    add $t1, $t1, $s2
    sw $t1, 48($s3)

***

Q

If the five variables `f, g, h, i, j` correspond to the five registers `$s0` through `$s4`, what is the compiled MIPS code?

    if (i == j) f = g + h; else f = g – h;

ANS

    bne $s3, $s4, ELSE
    add $s0, $s1, $s2
    j EXIT
    ELSE: sub $s0, $s1, $s2
    EXIT:

***

Q

Here is a traditional loop in C:

     while (save[i] == k)
            i += 1;

Assume that `i` and `k` correspond to registers `$s3` and `$s5` and the base of the array save is in `$s6`. What is the MIPS assembly code corresponding to this C segment?

ANS

    LOOP:sll $t1, $s3, 2
         add $t1, $t1, $s6
         lw $t2, 0($t1)
         bne $t2, $s5, EXIT
         addi $s3, 1
         j LOOP
         EXIT:

- `sll` is used to find the offset (`i*4`)
- we use `add` to find the address of `save[i]`, then in following `lw` we use `$t1` directly to access it

***

Q

Let’s turn the example on page 51 into a C procedure:

    int leaf_example (int g, int h, int i, int j)
    {
        int f;
        f = (g + h) – (i + j);
        return f; 
    }

What is the compiled MIPS assembly code?

ANS

    LEAF:add $sp, $sp, -12
         sw $t0, 8($sp)
         sw $t1, 4($sp)
         sw $t2, 0($sp)
         add $t0, $a0, $a1
         add $t1, $a2, $a3
         add $t2, $t0, $t1
         add $v0, $t2, $zero
         lw $t0, 8($sp)
         lw $t1, 4($sp)
         lw $t2, 0($sp)
         addi $sp, $sp, 12
         j $ra

- allocate new space on stack by **decreasing** `$sp`
- store old values of regs-to-use
- for return value, put it in `v0` and `v1`
- once old values restored, remove them from stack by **increasing** `$sp`
- jump back to main routine based on `$ra`

***

Q

    int fact (int n)
    {
        if (n < 1) return (1);
        else return (n * fact(n-1));
    }

What is the MIPS assembly code?

ANS

    fact:addi $sp,$sp,-8
         sw $ra, 4($sp) 
         sw $a0, 0($sp)
         slti $t0,$a0,1
         beq $t0,$zero,L1
         addi $v0,$zero,1
         addi $sp,$sp,8
         jr $ra
      L1:addi $a0,$a0,-1
         jal fact
         lw $a0, 0($sp)
         lw $ra, 4($sp)
         addi $sp, $sp,8
         mul $v0,$a0,$v0
         jr $ra

- first save the address of the function calls `fact` (could be itself) and the argument `n`
- `slti` compares `n` and `1`, put `1` in `$t0` if `n<1`
- if `$t0` is 0 then jumps to `L1`
- if not, then we reach the base case, put `1` as return value in `v0`, also adjust `$sp`
- return to caller
- for `L1`: decrement `$a0` (`n`) by 1, then call `fact`
- last 5 instructions are where `fact` returns, restore old values, adjust `$sp` and compute `n*fact(n-1)` and return to initial caller.

***

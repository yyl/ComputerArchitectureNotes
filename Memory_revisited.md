[revisted] Memory and caches
===

### Cache size

#### direct-mapped scheme

Given cache size as `2^n` blocks, block size as `2^m` words, word size as `32` bits, cache size (in bits):

	2^n x (2^m x 32 + (32 - n - m - 2) + 1)

- normally 8 bits = 1 byte
- above result divided by 8 we have cache size in byte
- `2^n` blocks => `n`-bit index field
- block size in cache: `2^m x word size + tag size + 1-bit validity`
	- `2^m x word size`: size of all words in one block
- bits of tag field: `32 - n - m - 2`
	- 32 bits address in total
	- `n` bits used as index
	- `m` bits used within each block
	- 2 bits used within each word
	- rest goes to tag

#### x-way set associative vs. tag size

Given cache size as `2^n` blocks, block size as `2^m` words, word size as `32` bits, with `2^k`-way associative scheme, 

	tag size = 2^n x (32 - (n - k) - m - 2) bits

- `2^n` blocks and `2^k`-way associative scheme result in `2^(n - k)` sets
- `n - k`: bits of index field
- bits of tag size increases along with `k` because it has to identify each block within each set

#### VM

[TODO]
# Computer Organization & Architecture — Complete Notes (OA Level)

## 1. Basic Computer Structure
- CPU (CU + ALU + Registers), Memory, I/O, System Bus (Data/Address/Control).
- **Von Neumann Architecture** — single memory for instructions + data; simpler but has "Von Neumann bottleneck" (can't fetch instruction & data simultaneously).
- **Harvard Architecture** — separate memory/buses for instructions & data; allows parallel access, used in DSPs, microcontrollers.
- **Modified Harvard** — separate caches for instruction/data but unified main memory (common in modern CPUs — e.g. separate L1i and L1d cache).

## 2. Registers (Detailed)
| Register | Full Form | Purpose |
|---|---|---|
| PC | Program Counter | Address of NEXT instruction to fetch |
| IR | Instruction Register | Holds CURRENTLY executing instruction |
| MAR | Memory Address Register | Holds address to be accessed |
| MDR/MBR | Memory Data/Buffer Register | Holds data being read/written |
| AC | Accumulator | Holds intermediate arithmetic results |
| SP | Stack Pointer | Points to top of stack |
| PSW/FLAGS | Program Status Word | Holds condition codes (zero, carry, overflow, sign) |
| GPR | General Purpose Registers | Temporary data storage |

## 3. Instruction Cycle (Detailed)
**Fetch → Decode → Execute → (Interrupt check) → repeat**
1. Fetch: `MAR ← PC`; `MDR ← Memory[MAR]`; `IR ← MDR`; `PC ← PC+1`.
2. Decode: CU interprets opcode in IR, identifies addressing mode & operands.
3. Execute: ALU/CU performs the operation; may involve additional memory access for operand fetch.
4. Interrupt check: CPU checks for pending interrupts after each instruction cycle.

## 4. Instruction Formats
- **Zero-address** — operates on stack (e.g. push/pop based, used in stack machines).
- **One-address** — uses accumulator implicitly (e.g. `ADD X` means `AC ← AC + X`).
- **Two-address** — e.g. `ADD A, B` means `A ← A + B`.
- **Three-address** — e.g. `ADD A, B, C` means `A ← B + C` (common in RISC/compilers' intermediate representation).

## 5. Addressing Modes (Detailed with example)
| Mode | Example | Meaning |
|---|---|---|
| Immediate | `MOV R1, #5` | Operand is the literal value |
| Direct | `MOV R1, 1000` | Operand is at memory address 1000 |
| Indirect | `MOV R1, @1000` | Address 1000 contains the ADDRESS of the operand |
| Register | `MOV R1, R2` | Operand is in register R2 |
| Register Indirect | `MOV R1, (R2)` | R2 contains address of operand |
| Indexed | `MOV R1, X(R2)` | Address = X + content of R2 |
| Base Register | Similar to indexed | Address = Base register + offset |
| Relative | `MOV R1, X(PC)` | Address = PC + offset (used in branch instructions) |

## 6. RISC vs CISC (Detailed)
| Feature | RISC | CISC |
|---|---|---|
| Instruction set | Small, simple | Large, complex |
| Instruction size | Fixed length | Variable length |
| Execution | Single cycle per instruction (mostly) | Multi-cycle |
| Registers | More GPRs | Fewer GPRs |
| Control unit | Hardwired | Micro-programmed |
| Addressing modes | Few | Many |
| Pipelining | Easy (uniform instructions) | Harder (variable complexity) |
| Examples | ARM, MIPS, RISC-V, SPARC | x86, VAX |
| Code density | Larger code (more instructions) | Smaller code (each instruction does more) |
| Compiler role | Heavy (must optimize instruction scheduling) | Lighter |

## 7. Control Unit Design
- **Hardwired CU** — implemented using combinational logic circuits/FSM; fast, but rigid/hard to modify or extend, used in RISC.
- **Micro-programmed CU** — control signals stored as microinstructions in Control Store (ROM); each machine instruction maps to a microprogram; flexible, easy to update/debug, but slower (extra memory access), used in CISC.
- **Horizontal vs Vertical Microprogramming**:
  - Horizontal — wide control word, each bit controls one signal directly, fast but large memory.
  - Vertical — encoded control word, needs decoder, slower but compact.

## 8. Pipelining (Detailed with Numerical Approach)
- Overlaps execution stages of multiple instructions to increase throughput (NOT latency of single instruction).
- Standard 5-stage: **IF → ID → EX → MEM → WB**.
- **Speedup formula (ideal)**: Speedup = k×n / (k + (n-1)), where k = number of stages, n = number of instructions (approaches k for large n).
- **Cycle time** = max stage delay (pipeline is only as fast as its slowest stage).

### Hazards (Detailed)
1. **Structural Hazard** — hardware resource conflict (e.g. single memory port for both instruction fetch and data access). Fix: separate instruction/data memory (Harvard-style cache), more functional units.
2. **Data Hazard** — instruction depends on result not yet available:
   - **RAW (Read After Write)** — true dependency, most common (e.g. `I2` reads register `I1` writes).
   - **WAR (Write After Read)** — anti-dependency.
   - **WAW (Write After Write)** — output dependency.
   - Fix: **Forwarding/Bypassing** (pass result directly from EX stage to next instruction's EX stage without waiting for WB), **Stalling** (insert bubble/NOP), **Compiler reordering**.
3. **Control Hazard** — caused by branch instructions (don't know next instruction to fetch until branch resolved).
   - Fix: **Branch Prediction** (static or dynamic), **Branch Target Buffer (BTB)**, **Delayed Branching** (execute instruction already in pipeline regardless of branch outcome), **Speculative Execution**.

## 9. Instruction-Level Parallelism (ILP)
- **Superscalar Architecture** — multiple instructions issued/executed per clock cycle using multiple execution units.
- **VLIW (Very Long Instruction Word)** — compiler packs multiple independent operations into one long instruction word (relies on compiler, not hardware, to find parallelism).
- **Out-of-Order Execution** — CPU executes instructions as operands become available rather than strict program order (still commits results in order — "in-order commit").

## 10. Flynn's Taxonomy (Classification of parallel architectures)
| Type | Meaning | Example |
|---|---|---|
| SISD | Single Instruction, Single Data | Traditional uniprocessor |
| SIMD | Single Instruction, Multiple Data | GPUs, vector processors |
| MISD | Multiple Instruction, Single Data | Rare, fault-tolerant systems |
| MIMD | Multiple Instruction, Multiple Data | Multi-core processors, distributed systems |

## 11. Memory Hierarchy (Detailed)
Registers (fastest, smallest, most expensive) → L1 Cache → L2 Cache → L3 Cache → Main Memory (RAM) → Secondary Storage (SSD/HDD) → Tertiary/Off-line storage (slowest, largest, cheapest).
- Exploits **Principle of Locality**:
  - **Temporal locality** — recently accessed data likely accessed again soon.
  - **Spatial locality** — nearby data likely accessed soon.

## 12. Cache Memory (Detailed with Numericals)
- **Hit ratio (h)** = hits / total accesses.
- **Average Memory Access Time (AMAT)** = Hit time + Miss rate × Miss penalty.
- **Effective Access Time** = h × Cache access time + (1−h) × Main memory access time (simplified version).

### Cache Mapping (Detailed)
1. **Direct Mapping** — `Cache line = (Block address) MOD (Number of cache lines)`. Simple, fast, but high conflict misses (two blocks mapping to same line evict each other repeatedly).
2. **Fully Associative Mapping** — block can go into ANY cache line; needs comparator for every line in parallel (expensive hardware); best hit ratio, most flexible.
3. **Set-Associative Mapping (k-way)** — cache divided into sets, each set has k lines; block maps to a specific SET but can go in any line within that set. Balances cost and performance (most real caches: 2-way, 4-way, 8-way).

### Cache Write Policies
- **Write-through** — every write updates cache AND main memory immediately; simpler, more reliable, but slower (more memory traffic).
- **Write-back** — write only updates cache; marks block "dirty"; written to memory only when evicted; faster, but more complex, risk if crash before write-back.

### Cache Replacement (for associative/set-associative)
LRU, FIFO, Random, Optimal (same concepts as OS page replacement).

## 13. Memory Types (Detailed)
- **SRAM** — flip-flop based, no refresh needed, faster, more expensive, used for cache.
- **DRAM** — capacitor based, needs periodic refresh, slower, cheaper, denser, used for main memory.
- **ROM** — non-volatile, read-only (mask ROM programmed at manufacture).
- **PROM** — programmable once by user (via fuses).
- **EPROM** — erasable via UV light, reprogrammable.
- **EEPROM** — electrically erasable, byte-level rewrite, basis for flash memory.

### Memory Interleaving
Splits memory into multiple banks so consecutive addresses are in different banks — allows parallel/overlapped access, increases effective bandwidth.

## 14. Number Systems & Computer Arithmetic

### Signed Number Representations
- **Sign-Magnitude** — MSB = sign, rest = magnitude; has two zeros (+0, −0); simple but complex arithmetic circuit.
- **1's Complement** — invert all bits for negative; has two zeros; "end-around carry" needed in addition.
- **2's Complement** — invert bits and add 1; single representation of zero; MOST used in modern systems; range for n bits: −2^(n-1) to 2^(n-1)−1.

### Booth's Algorithm (Multiplication of signed binary numbers)
Examines pairs of bits (current + previous) of multiplier:
- `10` → subtract multiplicand from accumulator.
- `01` → add multiplicand to accumulator.
- `00` or `11` → no operation.
Then arithmetic shift right. Repeat for n bits. Efficient for numbers with long runs of 0s or 1s.

### Floating Point (IEEE 754)
- **Single precision (32-bit)**: 1 sign bit + 8 exponent bits (bias 127) + 23 mantissa bits.
- **Double precision (64-bit)**: 1 sign + 11 exponent (bias 1023) + 52 mantissa.
- Value = (−1)^sign × 1.mantissa × 2^(exponent − bias).
- Special values: Exponent all 0s = denormalized/zero; Exponent all 1s = infinity/NaN.

### Adders
- **Half Adder** — adds 2 bits, outputs Sum & Carry (no carry-in).
- **Full Adder** — adds 3 bits (2 inputs + carry-in), outputs Sum & Carry-out.
- **Ripple Carry Adder** — chain of full adders, carry ripples through — simple but slow (carry propagation delay).
- **Carry Look-ahead Adder** — computes carries in advance using logic gates — faster, more hardware.

## 15. Interrupts & I/O (Detailed)

### Interrupt Handling Steps
1. Device raises interrupt signal.
2. CPU completes current instruction, checks interrupt line.
3. CPU saves current state (PC, registers, flags) — usually onto stack.
4. CPU jumps to Interrupt Service Routine (ISR) using interrupt vector.
5. ISR executes.
6. CPU restores saved state, resumes normal execution (`IRET`/`RETI`).

### Types
- **Hardware Interrupt** — from external device (maskable — can be disabled; non-maskable — always serviced, e.g. power failure).
- **Software Interrupt** — triggered by instruction (e.g. system call/trap).
- **Vectored Interrupt** — device provides address of its own ISR directly.
- **Non-vectored Interrupt** — CPU must poll all devices to determine interrupt source.

### I/O Techniques (Detailed)
| Technique | CPU Involvement | Speed | Use Case |
|---|---|---|---|
| Programmed I/O | CPU polls continuously (busy-wait) | Slow, wastes CPU | Simple, low-priority devices |
| Interrupt-driven I/O | CPU does other work, interrupted when device ready | Better CPU utilization | Moderate speed devices |
| DMA (Direct Memory Access) | CPU only initiates transfer, DMA controller handles rest | Best for bulk transfer | Disk, network transfers |

### DMA Modes
- **Burst Mode** — DMA controller takes over bus completely until transfer done.
- **Cycle Stealing Mode** — DMA controller takes bus for one cycle at a time, alternating with CPU.

## 16. Bus & Bus Arbitration
- **System Bus** = Data bus (bidirectional, width = word size) + Address bus (unidirectional, width determines addressable memory) + Control bus (carries control signals like read/write, interrupt, clock).
- **Bus Arbitration** (when multiple devices want bus access):
  - **Daisy Chaining** — devices connected in series, priority based on physical position.
  - **Centralized (Polling/Independent request)** — central arbiter grants access.
  - **Distributed arbitration** — devices collectively decide (e.g., using unique IDs).

## 17. Multiprocessor Architectures
- **Shared Memory (Tightly coupled)** — all processors access common memory (e.g. SMP — Symmetric Multiprocessing); needs cache coherence protocols (MESI).
- **Distributed Memory (Loosely coupled)** — each processor has own memory, communicate via message passing (used in clusters).
- **Cache Coherence** — ensures all processors see consistent view of shared memory; MESI protocol states: Modified, Exclusive, Shared, Invalid.

## 18. Common Numericals in OA
- Convert between binary, decimal, octal, hexadecimal (practice fast conversion).
- Compute 2's complement of a number, verify range.
- Compute effective address for various addressing modes given base/offset/index values.
- Compute AMAT given hit ratio, cache time, memory time.
- Compute cache size given number of sets, ways, block size (Cache size = sets × ways × block size).
- Compute number of bits for tag/set/offset in a given cache configuration.
- Pipeline speedup / throughput given number of stages and instructions (with/without stalls).
- Booth's algorithm trace for small multiplication examples (e.g., 7 × (−3)).

## 19. Common Traps / OA Gotchas
- CISC ≠ always slower — fewer instructions fetched can offset multi-cycle execution for some workloads; it's a design trade-off, not "RISC always wins."
- 2's complement has NO separate negative zero (unlike sign-magnitude/1's complement) — this is why it's preferred.
- Write-back is generally faster for repeated writes to same block; write-through is safer/simpler.
- Fully associative cache has ZERO conflict misses but is the MOST expensive to implement (needs parallel comparators for every line).
- A vectored interrupt does NOT require polling; non-vectored DOES.
- Structural hazards are resolved by hardware duplication, NOT by branch prediction (that's for control hazards) — don't mix these up in MCQs.

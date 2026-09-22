# Operating Systems — Complete Notes (OA Level)

## 1. Introduction
- **OS** = resource manager + interface between hardware and user.
- Functions: process management, memory management, file management, I/O management, security, protection, networking.

### Kernel Types
- **Monolithic Kernel** — entire OS (drivers, file system, scheduler) runs in kernel space as one program. Fast (no context switch between modules) but less stable (one bug can crash system). E.g. Linux, older UNIX.
- **Microkernel** — only essential services (IPC, basic scheduling, memory management) in kernel; rest (drivers, file systems) run in user space as servers. More stable, more overhead (message passing). E.g. QNX, Minix.
- **Hybrid Kernel** — combination, e.g. Windows NT, macOS (XNU).
- **Exokernel** — minimal abstraction, gives raw hardware access to applications.

### System Calls
- Interface for user programs to request kernel services.
- Categories: Process control (`fork`, `exec`, `exit`, `wait`), File management (`open`, `read`, `write`, `close`), Device management (`ioctl`), Information maintenance (`getpid`, `alarm`), Communication (`pipe`, `shmget`, `send`, `recv`).
- **Mode switch**: User mode ↔ Kernel mode via **trap/interrupt** during system call.

### Boot Process (brief)
BIOS/UEFI (POST) → Bootloader (GRUB) → Kernel loaded into memory → Init process starts → User space services start.

## 2. Process Management

### Process vs Program vs Thread (recap)
Program = passive code. Process = active execution instance with own address space. Thread = execution unit within a process sharing address space.

### Process States (detailed)
`New → Ready → Running → (Waiting ↔ Ready) → Terminated`
- New: process being created.
- Ready: waiting for CPU.
- Running: currently executing.
- Waiting/Blocked: waiting for I/O or event.
- Terminated: finished execution.
- **Suspended states** (used in medium-term scheduling): Suspended-Ready, Suspended-Blocked — process swapped out of main memory to disk.

### PCB (Process Control Block) fields
Process ID, Process State, Program Counter, CPU registers, CPU scheduling info (priority), Memory management info (page tables, segment tables, base/limit registers), Accounting info (CPU used, time limits), I/O status info (open files, allocated devices).

### Context Switching
Saving state of current process (PCB) and loading state of next process. Pure overhead — no useful work done during switch. Time depends on hardware support (registers, TLB flush cost).

### Process Creation
- `fork()` — creates child process, duplicate of parent (copy-on-write in modern systems); returns 0 to child, child PID to parent, -1 on failure.
- `exec()` — replaces process image with new program.
- `wait()` — parent waits for child to terminate.
- `exit()` — terminates process.
- **Zombie process** — child finished but parent hasn't called `wait()` yet; entry remains in process table.
- **Orphan process** — parent terminated before child; child adopted by `init`/`systemd`.

### Inter-Process Communication (IPC)
- **Shared Memory** — fastest, processes access common memory region, needs synchronization.
- **Message Passing** — `send()`/`receive()`, no shared memory needed, slower (kernel involved), used in distributed systems.
- **Pipes** — unidirectional, related processes (parent-child); named pipes (FIFOs) allow unrelated processes.
- **Message Queues** — kernel-maintained queue of messages.
- **Sockets** — communication across network.
- **Signals** — asynchronous notification (like SIGKILL, SIGTERM).

### Threads
- **User-level threads** — managed by user library, kernel unaware, fast to create/switch, but if one thread blocks, whole process blocks (unless using multiplexing).
- **Kernel-level threads** — managed by OS directly, kernel schedules each thread, slower to create/switch, but one thread blocking doesn't block others.
- **Multithreading Models**:
  - Many-to-One: many user threads → 1 kernel thread. Fast but no true parallelism, blocking issue.
  - One-to-One: each user thread → own kernel thread. True parallelism, but overhead of many kernel threads (Windows, Linux).
  - Many-to-Many: multiplexes m user threads to n kernel threads (n≤m). Best of both.

## 3. CPU Scheduling

### Scheduler Types
- **Long-term scheduler** — decides which jobs enter the ready queue (controls degree of multiprogramming).
- **Short-term scheduler (CPU scheduler)** — decides which ready process runs next (invoked frequently, must be fast).
- **Medium-term scheduler** — handles swapping (suspending/resuming processes).

### Dispatcher
Module that gives control of CPU to process selected by short-term scheduler — involves context switch, switching to user mode, jumping to correct location.

### Scheduling Criteria
CPU utilization (maximize), Throughput (maximize), Turnaround time (minimize), Waiting time (minimize), Response time (minimize).

### Algorithms with Numerical Approach
**FCFS** — Gantt chart in arrival order; suffers convoy effect (short process waits behind long one).

**SJF (non-preemptive)** — pick process with shortest burst time among arrived processes; optimal average waiting time among non-preemptive; needs burst time prediction (exponential averaging formula: `τ(n+1) = α·t(n) + (1-α)·τ(n)`).

**SRTF (preemptive SJF)** — preempt running process if new arrival has shorter remaining time.

**Round Robin** — fixed time quantum `q`; if quantum too large → behaves like FCFS; if too small → too many context switches (overhead dominates). Good response time, fair.

**Priority Scheduling** — lower number = higher priority (convention varies). Starvation solved by **aging** (gradually increase priority of waiting processes).

**Multilevel Queue Scheduling** — processes permanently assigned to a queue (e.g. system, interactive, batch) based on property; each queue has own algorithm; fixed priority between queues (can starve lower queues) or time-sliced between queues.

**Multilevel Feedback Queue** — processes CAN move between queues based on behavior (CPU-bound processes moved to lower priority queue); most general and flexible; parameters: number of queues, algorithm per queue, method to upgrade/demote.

### Numerical Example (practice pattern)
Given: Process, Arrival Time, Burst Time table → compute Completion Time, Turnaround Time (CT−AT), Waiting Time (TAT−BT), and average WT/TAT for FCFS, SJF, RR (given quantum), Priority.

## 4. Process Synchronization

### Critical Section Problem — 3 requirements
1. **Mutual Exclusion** — only one process in critical section at a time.
2. **Progress** — if no process in CS, selection of next process to enter can't be postponed indefinitely.
3. **Bounded Waiting** — limit on number of times other processes enter CS before a waiting process gets a turn.

### Peterson's Solution (2 process, software only)
Uses `flag[2]` and `turn` variable. Process i sets `flag[i]=true`, `turn=j`, waits while `flag[j] && turn==j`. Satisfies all 3 conditions but only works for 2 processes and requires strict memory ordering (may fail on modern CPUs without memory barriers).

### Hardware Solutions
- **Test-and-Set** instruction — atomic; returns old value and sets to true.
- **Compare-and-Swap (CAS)** — atomic; used in lock-free algorithms.

### Semaphores
- Integer variable accessed only via atomic `wait()`(P, decrement) and `signal()`(V, increment).
- **Counting Semaphore** — unrestricted domain, controls access to resource with multiple instances.
- **Binary Semaphore (mutex lock)** — value 0 or 1.
- Busy waiting version wastes CPU; can be implemented with blocking (waiting queue) to avoid busy-wait.

```
wait(S) {
  while (S <= 0) ; // busy wait
  S--;
}
signal(S) {
  S++;
}
```

### Monitors
High-level construct — a class-like structure where only one process can be active inside at any time (compiler-enforced mutual exclusion). Uses **condition variables** with `wait()` and `signal()` for waiting on conditions inside.

### Classic Synchronization Problems
**Producer-Consumer (Bounded Buffer)**
Semaphores: `mutex=1` (binary, for buffer access), `empty=n` (counts empty slots), `full=0` (counts full slots).
```
Producer: wait(empty); wait(mutex); produce; signal(mutex); signal(full);
Consumer: wait(full); wait(mutex); consume; signal(mutex); signal(empty);
```

**Readers-Writers Problem**
Multiple readers can read simultaneously; writer needs exclusive access. Variants: Reader-priority, Writer-priority, Fair (no starvation).

**Dining Philosophers**
5 philosophers, 5 forks, each needs 2 forks to eat → circular wait → deadlock possible.
Solutions: allow max 4 philosophers at table simultaneously; asymmetric solution (odd philosopher picks left first, even picks right first); pick both forks only if both available (atomic).

## 5. Deadlocks

### 4 Necessary Conditions (Coffman conditions)
1. Mutual Exclusion — resource non-shareable.
2. Hold and Wait — process holds resource while waiting for another.
3. No Preemption — resource can't be forcibly taken.
4. Circular Wait — circular chain of processes waiting on each other.

### Deadlock Handling Strategies
1. **Prevention** — ensure at least one condition can never hold.
   - Eliminate Hold-and-Wait: require process to request all resources upfront.
   - Allow Preemption: if a process holding resources requests another that can't be allocated, preempt its held resources.
   - Eliminate Circular Wait: impose total ordering of resource types, request only in increasing order.
2. **Avoidance** — allow all 4 conditions but ensure system never enters unsafe state.
   - **Banker's Algorithm**: needs Max, Allocation, Need matrices + Available vector. Checks if a request can be granted while keeping system in a **safe state** (exists a sequence of all processes that can finish).
3. **Detection & Recovery** — allow deadlock to occur, detect using **Wait-for graph** (cycle = deadlock, for single instance resources) or resource allocation graph algorithm (multi-instance), then recover by:
   - Process termination (abort all or one at a time).
   - Resource preemption (rollback, selecting victim, avoiding starvation).
4. **Ignore (Ostrich Algorithm)** — used by Linux/Windows; assume deadlocks are rare, cheaper to reboot than to prevent always.

### Banker's Algorithm — Safety Check Steps
1. Work = Available; Finish[i] = false for all.
2. Find i such that Finish[i]==false and Need[i] ≤ Work.
3. Work += Allocation[i]; Finish[i] = true; repeat.
4. If all Finish[i]==true → safe state.

## 6. Memory Management

### Contiguous Allocation
- Fixed partitioning — internal fragmentation.
- Variable partitioning — external fragmentation; solved by **compaction** (costly).
- Allocation strategies: **First Fit**, **Best Fit**, **Worst Fit**.

### Paging
- Logical memory divided into fixed-size **pages**; physical memory into **frames** (same size).
- **Page Table** maps page number → frame number.
- Logical Address = (Page Number, Offset); Physical Address = (Frame Number, Offset).
- **TLB (Translation Lookaside Buffer)** — cache for page table entries, speeds up address translation. TLB hit = fast; TLB miss = page table walk.
- **Effective Access Time (EAT)** = hit_ratio × (TLB access + memory access) + miss_ratio × (TLB access + 2×memory access).
- **Multilevel Paging** — page table itself paged (used when page table too large for single-level, e.g. 32/64-bit systems).
- **Inverted Page Table** — one entry per physical frame (not per logical page), saves memory but slower lookup (needs hashing).
- No external fragmentation; may have internal fragmentation (last page).

### Segmentation
- Divides program into logical segments (code, stack, data, heap) — variable size.
- Segment table: base + limit per segment.
- Logical Address = (Segment Number, Offset).
- Matches programmer's view; suffers external fragmentation.

### Segmentation with Paging
Each segment further divided into pages — combines benefits, common in real systems (x86 protected mode).

### Virtual Memory
- Allows execution of processes not fully in memory; uses disk as extension of RAM.
- **Demand Paging** — pages loaded only when referenced (page fault triggers load).
- **Page Fault** — accessing a page not in memory → trap to OS → OS loads page from disk → restart instruction.
- **Copy-on-Write (COW)** — parent & child share pages after fork(); copied only when written to.

### Page Replacement Algorithms
- **FIFO** — oldest page out; suffers **Belady's Anomaly** (more frames can cause MORE page faults).
- **Optimal (OPT/MIN)** — replace page not used for longest time in future (theoretical benchmark, can't implement in practice).
- **LRU** — replace least recently used; implemented via counters or stack.
- **LFU** — replace least frequently used.
- **MFU** — replace most frequently used (rare).
- **Second Chance (Clock) Algorithm** — approximates LRU using reference bit, cheaper than true LRU.

### Thrashing
High degree of multiprogramming → processes spend more time paging than executing → CPU utilization drops → OS may add MORE processes (mistaking low utilization for need for more) → worsens thrashing.
- **Solution**: Working Set Model (track set of pages a process actively uses), Page Fault Frequency (PFF) monitoring, reduce multiprogramming degree.

### Fragmentation
- **Internal** — allocated memory slightly larger than requested (wasted inside partition).
- **External** — free memory exists but scattered in small chunks, can't satisfy a large request.

## 7. File Systems

### File Attributes
Name, identifier, type, location, size, protection, timestamps.

### File Allocation Methods
- **Contiguous** — fast sequential + random access, but external fragmentation, hard to grow file.
- **Linked** — each block points to next; no external fragmentation, but slow random access, pointer overhead, reliability issue if pointer corrupted.
- **Indexed** — index block holds pointers to all data blocks; supports direct access; overhead of index block; for very large files use multilevel/combined indexing (like UNIX inodes with direct, single indirect, double indirect, triple indirect blocks).

### Free Space Management
Bit vector, linked list, grouping, counting.

### Directory Structures
Single-level, Two-level, Tree-structured, Acyclic-graph (allows sharing via links), General graph (needs cycle detection/garbage collection).

### Disk Structure
Platters, tracks, sectors, cylinders. Disk access time = seek time + rotational latency + transfer time.

### RAID Levels (Redundant Array of Independent Disks)
| Level | Description |
|---|---|
| RAID 0 | Striping, no redundancy, high performance, no fault tolerance |
| RAID 1 | Mirroring, full redundancy, 50% storage efficiency |
| RAID 5 | Striping with distributed parity, fault tolerant (1 disk failure), good balance |
| RAID 6 | Like RAID 5 but double parity, tolerates 2 disk failures |
| RAID 10 | Combination of striping + mirroring |

## 8. Disk Scheduling
Given a sequence of requests and initial head position, compute total head movement.
| Algorithm | Idea |
|---|---|
| FCFS | Service in arrival order |
| SSTF | Nearest request serviced next (can cause starvation) |
| SCAN (Elevator) | Move in one direction servicing requests, reverse at end |
| C-SCAN | Only service in one direction; jump back to start without servicing on return |
| LOOK | Like SCAN but reverses at last request (not disk boundary) |
| C-LOOK | Like C-SCAN but reverses at last request |

## 9. Common Numerical/Conceptual Traps in OA
- Belady's Anomaly only occurs in FIFO (not LRU/Optimal).
- In RR, if quantum ≥ max burst time → equivalent to FCFS.
- Effective memory access time calculations with TLB hit ratio.
- Producer-Consumer semaphore order matters (wait(empty) before wait(mutex) in producer).
- Difference: Preemptive vs Non-preemptive scheduling — preemptive can interrupt running process, non-preemptive cannot.

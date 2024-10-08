# HW4 Answer the following questions about threading and memory access

[Register](https://en.wikipedia.org/wiki/Processor_register)
[Cache](https://en.wikipedia.org/wiki/Cache_(computing))

1. Basic facts
    a. What does a computer's clock speed regulate? The speed of executing instructions
    b. We double the clock speed
      1. memory read is relatively unchanged
      2. memory write is relatively unchanged
      3. reading from cache is faster
      4. multiplication speed is faster
    c. Speed of each instruction in clock cycles.
      1. add %rax, %rbx **1 clock cycle**
      2. mul %rax, %rbx **3-5 clock cycles**
      3. div %rcx       **20-50 clock cycles**
      4. shl $1, %rax  **1 clock cycle**
    d. Assume the following memory read instructions are in cache,
       but the location being read is new. timing is 46-45-45
      1. mov (%rsi), %rax       **1 clock cycle**
      2. vmovdqa (%rsi), %ymm0 **2 clock cycles**
    e. Why can't we just double the clock speed to make our computer faster?
**Heat dissipation and power consumption would increase significantly, and memory access times do not scale with clock speed.**
    f. What is the fastest memory in a computer? CPU registers
    g. What is the 2nd fastest memory? L1 Cache
    h. What makes main memory (DRAM) so much slower than the fastest memory?
**DRAM has higher access latency and slower transistor switching compared to cache.**
    i. How many integer registers are there on an x86-64 machine? **16**
    j. How many vector registers are there on an AVX2 machine? **16**
    k. How many integer registers are there on an ARM64 machine? **32**
    l. Why might the ARM designers have chosen differently than Intel?
**ARM focuses on power efficiency and simplicity, which is important for mobile and embedded systems, whereas Intel prioritizes high performance.**
    m. A special called RIP on intel rip. What does it stand for: **Instruction Pointer**
    n. Look up what it does: **RIP holds the memory address of the next instruction to be executed.**
    o. What is the register RSP on intel?
 **Stack Pointer (points to the top of the current stack in memory)**
    p. What is L1 cache on x86 architecture? number of cores: **1** , size: **32kb-128kb**
    q. What is L2 cache on x86 architecture? number of cores **1**, size **256kb-1MB**
    r. What is L3 cache on x86 architecture? number of cores **Shared among all cores**,size:**2 MB - 32 MB**
    s. Approximately how long does it take light to travel 30cm?
** 1 nanosecond**

2. Class Survey
   a. Enter your name into a row of the spreadsheet and complete the data for your computer.
   b. If you have more than one computer you can pick the one you use. You may enter more than one.
https://docs.google.com/spreadsheets/d/10DiQJcTMTqcE1JjSKFx0AWUcOAqu75cQUcKsF0jtxBg/edit?usp=sharing

2. Basics of Multiprocessing
    a. What is a process?
   
    **A process is an independent program that is running in memory. It has its own allocated resources, such as memory space and file handles, and operates in its own execution context.**
   
    b. What is a thread?
   
   **A thread is a smaller unit of execution within a process. Multiple threads within a process share the same memory space but execute independently, allowing for parallelism within a single process.**
   
    c. Every thread requires at a minimum sp, pc/rip. Explain:
   
**Each thread needs a Stack Pointer (sp), which points to the top of its stack (local data storage), and a Program Counter (pc) or Instruction Pointer (RIP), which keeps track of the next instruction to execute. These registers allow the thread to maintain its own independent flow of execution within a shared process.**

   d. A computer with 4 cores is running a job.
       1 thread, t=10s, 2 threads t=5s, 4 threads t=3s
       Neglecting hyperthreading, Why might it not be a good idea to run with 8 threads?
   
   **Running 8 threads on a 4-core machine can cause contention and context switching overhead because the number of threads exceeds the available cores. As a result, the operating system must frequently swap threads in and out of the cores, leading to inefficiencies and potentially diminishing returns in performance.**

4. Explain the benchmark results for memory_timings.s.
   a. For each function run explain (in one line) what it is attempting to measure.

**read_one: Reads a single 64-bit (8-byte) word from memory repeatedly and counts down a loop.
This tests the performance of repeatedly accessing the same memory location.**

**read_memory_scalar: Reads sequential 64-bit words from memory, advancing the pointer by 8 bytes each time (since 64-bit = 8 bytes). This tests sequential memory reads using scalar (regular) instructions**

**read_memory_every2: Reads every second 64-bit word (skipping one) for even words first, and then reads odd words.
This could test how skipping some data impacts memory performance due to cache misses or memory access patterns**

**read_memory_everyk: Skips k memory locations and reads words in a non-continuous manner.
Useful for testing memory access patterns that are less sequential and could affect cache efficiency**

**read_memory_sse: Reads 128 bits (16 bytes) from memory using SSE instructions (movdqa).
This tests sequential reads using SIMD (Single Instruction, Multiple Data) instructions, which can read larger chunks of memory at once compared to scalar instructions**

read_one_avx: Read**s 256 bits (32 bytes) from memory using AVX instructions (vmovdqa).
Like the SSE version, but this tests performance using AVX instructions, which can read even larger memory chunks**

**read_memory_avx: Reads 256 bits (32 bytes) sequentially using AVX instructions.
This is similar to the scalar and SSE versions but takes advantage of AVX for faster memory reads**

**read_memory_sse_unaligned and read_memory_avx_unaligned: These test reading unaligned data with SSE and AVX. Unaligned access means the memory is not aligned to the data bus width, which generally results in slower access times**

**write_one: Writes to a single memory location repeatedly, looping over the same 64-bit word.
This benchmarks the performance of repeatedly writing to the same memory address**

**write_one_avx: Writes 32 bytes to a single memory location using AVX instructions.
Benchmarks the performance of AVX in writing memory**

**write_memory_scalar: Writes 64 bits (8 bytes) to memory sequentially using scalar instructions.
Tests how fast memory writes are when using standard instructions**

**write_memory_sse: Writes 128 bits (16 bytes) sequentially using SSE instructions.
Tests the use of SIMD for faster memory writes**

**write_memory_avx: Writes 256 bits (32 bytes) sequentially using AVX instructions.
Measures write performance using AVX****

   b. Why is write_one so much slower than read_one? **Because write operations are slower due to cache coherency, memory write policies and store buffers**
   c. Why is read_memory_avx faster than read_memory_scalar if both are reading the same amount of memory sequentially? **AVX allows reading of larger chunks of memory at once. AVX reads 256 bits at a time while scalar instructions process only 64 bits at a time by using SIMD instructions**
   d. Run on your computer (or a lab computer if yours is an ARM or you have some other problem). Report the results.
     1. Extra credit: if you write your own assembly code and test a different architecture, + 50%
     2. Find the CPU and memory configuration for the machine you tested. This can be About... in windows, or in linux you can use lscpu and cat /proc/cpuinfo
6. Explain what a cache is **small fast memory located near the CPU, designed to store memory that is frequently accessed. Intermediate buffer between CPU and RAM, improving access time for data used repeatedly**
   a. Explain what a cache miss is? **Cache miss is when the CPU needs is not present in the cache, forcing the system to fetch it from slower main mem. Introducing latency while CPU waits for data**
   b. Why is read_memory_repeated1 faster than read_memory_repeated2?

7. Why is it so important to understand memory performance for parallel computing? **Memory performace is critical in parallel computing because multiple threads/processes need to access memory at the same time. Memory being slow or inefficient its a bottleneck not allowing parallelism to occur.**
9. Why does pipelining on a modern CPU make benchmarking so difficult? **Pipelining allows the CPU to execute several instructions at different parts of the pipeline meaning that the execution of the instructions overlap increasing throughput, but hindering benchmarking.**
10. Why doesn't AMD define their own extensions to the instruction set, like more registers? **x86 ecosystem allows AMD processors to run a wide variety of software that already exists without changing. showing significant changes like more registers requiring more software developers for the AMD chips.**
11. Why doesn't a CPU manufacturer design a computer with 1 million registers? **Registers are expensive given the silicon, and they are located close to CPU. It would increase the complexity of design so much. 1 million registers would be hard also in terms of context changing. They would need to be constantly saved and restored**

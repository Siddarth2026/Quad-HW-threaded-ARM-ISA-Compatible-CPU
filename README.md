# Quad Hardware-Threaded ARM ISA-Compatible CPU

## Overview

This repository contains the design and implementation of a **Quad hardware-threaded, ARM ISA-compatible pipelined processor**. The design extends the previous single-threaded 5-stage pipeline by introducing four hardware threads, allowing the processor to 
execute instructions from multiple threads in an interleaved manner. This approach improves pipeline utilization by reducing the performance impact of hazards and long-latency operations. 
 

The processor is written in Verilog and targets Xilinx FPGAs. The implementation has been tested on the NetFPGA v2 platform.

This README focuses on the modifications made to extend the original 5-stage pipelined CPU into a quad hardware-threaded processor. For details on that project, check out this link: https://github.com/Siddarth2026/ARM-compatible-5-stage-pipelined-CPU.git

## Need for Multithreading

A single-threaded pipeline can only process one instruction stream at a time. Whenever that stream stalls, every stage behind it also stalls and must wait until the hazard is resolved. These stalls are unavoidable in a single-threaded pipeline and reduce overall pipeline utilization.

This idle time can be reduced with multithreading. There are two main types of multithreading: **software multithreading** and **hardware multithreading**. Software multithreading relies on the compiler or software to rearrange instructions and schedule multiple threads efficiently. Hardware multithreading provides multiple instruction streams, allowing the processor to switch between threads and continue executing instructions even when one thread stalls.

Hardware multithreading hides latency by allowing other threads to execute while one thread is stalled, rather than leaving the pipeline idle. Since the probability of all four threads stalling or being flushed at the same time is low, the overall likelihood of the pipeline stalling is greatly reduced. This improves pipeline utilization and increases overall throughput without requiring additional execution units. This is more beneficial for designs with frequent memory accesses and branch instructions, where stalling and flushing occur often.

## Architectural Changes to a Single-Threaded Pipeline
![Quad HW threaded pipeline datapath](4Tpipelinedatapath-clean.png)
There are a few major changes to the single-threaded 5-stage pipeline model to incorporate hardware multithreading. The new blocks and modifications to the existing design are listed below.

* **Thread ID:** Round-robin multithreading is implemented with four threads. Whenever the thread ID is enabled, the current thread ID increments by one, wraps from 3 back to 0, and repeats. The current thread ID is propagated through all pipeline stages to identify which thread is executing at each stage. This information is also used by other blocks to ensure data from one thread is never mixed with that of another.

* **Program Counter (PC):** Instead of a single program counter, four program counters are used, with the active PC selected based on the current thread ID. During stalling or branching, only the PC belonging to the affected thread is updated, while the remaining threads continue execution normally. This keeps each instruction stream independent.

* **Register File:** The single register file is replaced with four independent register files, one for each thread. The current thread ID selects which register file is used for register reads and write-back operations. Since each thread has its own register file, there is no context-switching overhead or shared register state to save or restore.

* **Per-Thread NZCV Flags:** The single set of NZCV flags is extended to four independent sets, one for each thread. This prevents flag updates from one thread from affecting another during conditional branch evaluation. Only the NZCV flags from the current thread are checked against the instruction's condition field.

* **Hazard Detection Unit:** The Hazard Detection Unit is modified so that hazards are detected only between instructions belonging to the same thread. This prevents unnecessary stalls caused by dependencies between different threads.

* **Forwarding Unit:** The Forwarding Unit is similarly modified to forward data only between instructions from the same thread. This ensures that forwarded values always belong to the correct thread and prevents data from one thread from being forwarded to another.


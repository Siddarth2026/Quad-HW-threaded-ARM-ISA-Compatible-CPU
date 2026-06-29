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

## Architectural changes to a single-threaded pipeline


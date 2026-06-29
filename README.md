# Quad Hardware-Threaded ARM ISA-Compatible CPU

## Overview

This repository contains the design and implementation of a **Quad hardware-threaded, ARM ISA-compatible pipelined processor**. The design extends the previous single-threaded 5-stage pipeline by introducing four hardware threads, allowing the processor to 
execute instructions from multiple threads in an interleaved manner. This approach improves pipeline utilization by reducing the performance impact of hazards and long-latency operations. 
 

The processor is written in Verilog and targets Xilinx FPGAs. The implementation has been tested on the NetFPGA v2 platform.

This README focuses on the modifications made to extend the original 5-stage pipelined CPU into a quad hardware-threaded processor. For details on that project, check out this link: https://github.com/Siddarth2026/ARM-compatible-5-stage-pipelined-CPU.git

## Need for multithreading




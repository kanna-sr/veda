---
title: "SMX Threads"
date: 2026-05-20
description: "SMX Threads implementation in Software and Hardware"
---

The SMX does work for threads, not processes.  
 
![SMX](SMX.png)

## Software Threads

The programmer defines the number of thread blocks for a process and the number of threads per block. Per block there can be a maximum of 1 or 1024 (32 x 32) threads.  The SMX queues up thread blocks to be executed. 

- **Thread Blocks** - defined by software engineer and has x number of threads per block

## Hardware Threads

The SMX hardware executes threads as a WARP. The SMX is the factory floor, whereas a WARP is a cleaning crew on the floor. The SMX can have up to 64 WARPS and each WARP has 32 threads. The SMX has up to four Warp Schedulers (which is part of the SMX scheduling unit) to determine which warp and thread blocks should be executed. 

![Warp Scheduler](SMX_Scheduling.png)



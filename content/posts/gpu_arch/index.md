+++
date = '2026-04-30T18:47:16-04:00'
draft = false
title = 'NVIDIA GeForce GPU Architecture'
+++

## Intro
This blog post highlights the important components in a GeForce GPU and how it interfaces with the CPU. 

The "Host" CPU interfaces with the "Device" GPU through PCIe busses. The CPU writes information to the GPU's global memory VRAM. The GPU unit has read only constant/texture memory regions. Constant memory is always available to threads while texture memory is for 2d spatial locality like image processing or computer vision tasks. Each GPU also contains 100+ SMX devices which do heavy lifting work. If the SMX does not have data it needs, it will access the L2 cache or pass the request to the multiple memory controllers on the device to get data from global memory.

<br><br>

![CPU and GPU Architecture](cpu_gpu.png) <br><br>

#### Device
    * CPU: manages GPU via commands, kernel codes and data via PCIe bus
    * Global Memory: primary VRAM for GPU
    * Constant & Texture memory: read only
         * Constant: available to threads
         * Texture: 2D spatial locality (image processing, computer vision)
    * Streaming Multiprocessors (SMX): main workhorse
    * L2 cache: shared cache that's used by all SMXs
          * Don't nee to use global memory then
    * Memory controller
	 * Is used when SMX request to L2 cache fails
	 * Queries VRAM
	 * Ensures SMX cores and L2 cache have data

#### Device: Memory Controller
The goal of the memory controller is to ensure the SMXs always have data since they are constantly doing work. When the SMX requests data, it goes to the L2 cache, but when that fails the L2 cache will forward the request to the memory controller to get from global memory/VRAM.

    * Each memory controller maps to a part of the L2 cache and a corresponding portion of physical VRAM
    * The L2 cache will determine which memory controller to query based on the address request
    * The memory controller will then perform address translation between logical memory address and physical rows
    * Manages data between SMX and Global Memory/VRAM
    * SMX will do a lot of calculations and need more data
        * Ensures SMX cores and L2 cache have data


# Streaming Multiprocessor (SMX)
The SMX can be logistically broken down into three processing units: scheduling, execution and networking/memory.

### SMX: Scheduling
The scheduling units load the instruction from the instruction cache and the dispatch unit determines which execution component to send it to. the Warp Scheduler manages threads.

![SMX Scheduling Units](SMX_Scheduling.png)

    * Instruction cache - stores instruction being executed
    * Warp scheduler manages threads and decides which ones should be executed
    * Dispatch unit - gets instructions from scheduler and send instruction to execution components of SMX (Core, DPU)

### SMX: Execution
The exeuction units read data from register file, do some processing and writes the answer back to register file data bus. The instruction being executed determines which hardware to use. The LD/ST units are used to move data from memory (L1, L2, Global) to the register file.

![SMX Execution](SMX_Execution.png)
   
     * Register File: staging area for data. Queries L1 cache or others if it doesn't have necessary data
     * Core (CUDA Cores): standard integer/floating point arithmetic
     * DPU (Double Precision Unit): specialized FP64 (double precision) for usually scientific sims
     * SFU (Special Function Units): complex transcendental math (sin, cos, square root, logs)
     * LD/ST - move data between register file and memory (L1, L2, Global Memory)
	   * There’s ld/st for data from cache before instruction execution

### SMX: Networking and Memory
![SMX Networking and Memory](SMX_Networking.png)
   * Interconnect Network: connects execution units to local memory and cache
   * Shared Memory/L1 Cache: high speed, low latency memory shared by all threads on SMX
   * Read-only Cache: read only during kernel execution
   * Tex (Texture Units): texture mapping, filtering, address operations


---
title: "NVIDIA GeForce GPU Architecture"
description: "Deep dives into GPU internals, NVISA reverse engineering, and hardware security."
---

This blog post highlights the important components in a GeForce GPU and how it interfaces with the CPU.

The "Host" CPU interfaces with the "Device" GPU through PCIe buses. The CPU writes information to the GPU's global memory (VRAM). The GPU has read-only constant and texture memory regions — constant memory is always available to threads, while texture memory is optimized for 2D spatial locality tasks like image processing and computer vision. Each GPU also contains 100+ SMX units that do the heavy lifting. If an SMX does not have the data it needs, it accesses the L2 cache or passes the request to a memory controller to fetch from global memory.

![CPU and GPU Architecture](cpu_gpu.png)

## Device

- **CPU** — manages the GPU via commands, kernel code, and data over the PCIe bus
- **Global Memory** — primary VRAM for the GPU
- **Constant Memory** — read-only; always available to threads
- **Texture Memory** — read-only; optimized for 2D spatial locality (image processing, computer vision)
- **Streaming Multiprocessors (SMX)** — the main workhorse units of the GPU
- **L2 Cache** — shared cache used by all SMXs, avoiding round-trips to global memory
- **Memory Controller** — queried when an SMX request to the L2 cache fails; handles address translation and fetches data from VRAM

## Device: Memory Controller

The goal of the memory controller is to ensure the SMXs always have data, since they are constantly doing work. When an SMX requests data, it first goes to the L2 cache — if that fails, the L2 cache forwards the request to the memory controller to retrieve from global memory/VRAM.

- Each memory controller maps to a portion of the L2 cache and a corresponding section of physical VRAM
- The L2 cache determines which memory controller to query based on the requested address
- The memory controller performs address translation between logical and physical memory addresses
- Manages data flow between SMX units and Global Memory/VRAM

## Device: Streaming Multiprocessor (SMX)

The SMX can be logistically broken down into three processing units: scheduling, execution, and networking/memory. See the [SMX Deep Dive](/posts/gpu_arch/smx/) for a full breakdown.

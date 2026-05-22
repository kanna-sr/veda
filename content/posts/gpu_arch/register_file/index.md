---
title: "Register File"
date: 2026-05-22
description: "Register Files on SMX"
---

![SMX](register_file.png)

## Register File

Once the WARP scheduler determines which WARP (e.g., 32 threads to execute), the scheduler sends the instruction information from the instruction cache to the dispatch unit. Once the dispatch unit receives the instruction, the cores begin to do work. For the instruction "ADD r5, r4, r1, rz" which is equivalent to r5 = r4+r1, the cores would read r4 and r1 from the register file, perform the arithmetic and then store the output back to the register file's r5. 

If there is information missing from the register file, the LD/ST units are used to move data between from the L1, L2 or Global Memory to the register file.

The register file is split physically and logically.

## Physical Split

The register file is split into 4+ "banks" for multi-operand instructions, e.g., multiply and add. Splitting in into banks avoids having a port per thread.

Bank System:
- **Bank 0**: r0, r4, r8
- **Bank 1**: r1, r5, r9
- **Bank 2**: r2, r6, r10
- **Bank 3**: r3, r7, r11

Reading r0 and r3 would be simultaneous because it access Bank 0 and 3. However, accessing r1 and r9 would be a performance hit.

## Logical Split

Please see [GPU Threads](/veda/posts/gpu_arch/threads/) for information on how the register file is split logically


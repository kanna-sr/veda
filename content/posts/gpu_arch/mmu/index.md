---
title: "GPU MMU"
date: 2026-05-22
description: "GPU Memory Management Units (GMMU)"
---

![MMU](mmu.png)

## GPU Memory Management Unit (GMMU)

SMXs have Memory Management units (GMMU) to perform virtual to physical address translations via GPU Page Tables. 

Each GPU process has a page table and has multiple levels (NVIDIA has 5). GMMU takes 49-bit virtual address and uses information encoded in it to walks through page table entry (PTE) to get physical address. 





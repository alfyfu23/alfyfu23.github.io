---
title: COD25 — 用 SystemVerilog 设计一个 CPU
layout: post
author: alfyfu
date: 2026-06-02 16:00:00 +0800
categories: [课程]
tags: [SystemVerilog, CPU, 计算机组成原理, 硬件设计]
---

## 课程背景

计算机组成原理课程的大作业：**用 SystemVerilog 从零实现一个处理器**。

这不是写代码模拟 CPU 行为，而是真正的硬件设计 — 最终可以综合到 FPGA 上运行。

## 设计概览

### 目标

实现一个支持基本指令集的流水线处理器，包括：

- 取指、译码、执行、访存、写回五级流水线
- 数据缓存与指令缓存
- 基本的中断和异常处理

### 工具链

- **语言**：SystemVerilog
- **仿真**：Verilator / ModelSim
- **综合**：Vivado（FPGA）
- **协作**：Git

## 核心模块

### 1. 流水线设计

五级流水线是 CPU 设计的核心。最大的挑战是**冒险处理**：

```systemverilog
// 数据冒险检测
always_comb begin
    if (rs1 == ex_rd && ex_regwrite && rs1 != 0)
        forward_a = 2'b10;  // 从 EX/MEM 前递
    else if (rs1 == mem_rd && mem_regwrite && rs1 != 0)
        forward_a = 2'b01;  // 从 MEM/WB 前递
end
```

### 2. Cache 设计

直接映射缓存，解决 CPU 与内存的速度差距：

- **指令 Cache**：减少取指延迟
- **数据 Cache**：加速访存操作

### 3. 控制单元

译码阶段需要根据指令类型生成大量控制信号。

## 团队协作

这是小组项目，我们做了明确的分工：

| 成员 | 负责模块 |
|------|----------|
| 我 | 流水线数据通路 + 冒险处理 |
| 成员 A | Cache 模块 |
| 成员 B | 控制单元 + 指令译码 |
| 成员 C | 测试与仿真 |

用 Git 管理代码，每个模块一个分支，合并时做集成测试。

## 遇到的挑战

### 挑战 1：流水线冒险

数据冒险和控制冒险是最难的部分。

> **数据冒险**：通过前递（forwarding）解决，检测结果从 EX 和 MEM 阶段前递到输入端。
> **控制冒险**：分支预测 + 冲刷流水线。
{: .prompt-tip }

### 挑战 2：时序收敛

综合到 FPGA 后，某些关键路径时序不满足。

> **方案**：拆分关键路径，插入流水线寄存器。
{: .prompt-tip }

### 挑战 3：Debug 困难

硬件 debug 和软件完全不同，不能加 print 看变量值。

> **方案**：写详尽的 testbench，用波形（waveform）逐步检查信号。
{: .prompt-warning }

## 收获

- 深入理解了 CPU 的工作原理，不再只是书本上的概念
- 学会了硬件描述语言和硬件思维方式
- 体验了硬件项目的团队协作流程

> 课程代码（个人实验部分）：[GitHub - cod25-personal](https://github.com/alfyfu23/cod25-personal)
> 小组大作业：[GitHub - cod25-group](https://github.com/alfyfu23/cod25-group)
{: .prompt-info }

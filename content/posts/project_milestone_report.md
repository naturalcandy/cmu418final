---
title: "Project Milestone Report"
date: 2025-04-15T22:40:54-04:00
draft: false
---

Our 15-418 Milestone Report

<!--more-->

# Milestone Report

## Summary of Progress So Far

Over the past few weeks, we have made considerable progress toward our objective of building a GPU-accelerated GraphBLAS framework. As of this milestone, we have implemented:

* A fully functional API

* A working kernel sequence staging system and backend JIT compiler:
  * Generates and executes fused CUDA kernels based on the scheduled operation graph.
  * This is done to eliminate costly per-iteration kernel launches in iterative graph algorithms by staying resident on the GPU

* Element-wise operations (Add, Sub, Mul, Div) supported on the GPU

* Memory allocator and buffer tracker supporting multiple data types along with efficient memory alignment

* CSR format conversion kernel for dense matrix input, complete with correctness testing

* Row-based parallel CSR SpMV kernel and correctness validation

* Warp-collaborative CSR SpMV implementation with dynamic thread assignment and shuffle-based reduction

In addition to implementing these components, we conducted performance benchmarking comparing our GPU-accelerated CSR SpMV implementations against a sequential CPU-based approach. From our profiling we were able to see first-hand the memory-bound nature of SpMV. These insights motivate us to explore more efficient parallel execution strategies and additional specialized matrix storage formats that are suited to the irregular memory access patterns characteristic of graph algorithms.

## Updated Project Schedule (April 15 - April 29)

| Timeframe       | Tasks                                                                                                           | Owner |
|-----------------|------------------------------------------------------------------------------------------------------------------|--------|
| Apr 15 - Apr 17 | Optimize Vector CSR kernel implementations and look into different sparse matrix formats suited for different graph sparsity types. | Devi   |
| Apr 17 - Apr 19 | Enhance persistent kernel architecture to support dynamic termination conditions (e.g., convergence thresholds) rather than fixed iteration counts for iterative graph algorithms. | Noli   |
| Apr 19 - Apr 21 | Implement specialized computational kernels for different semiring operations (MIN-PLUS, LOGICAL-OR-AND, etc.) to optimize performance for specific graph algorithms. | Noli   |
| Apr 21 - Apr 22 | Implement some example graph algorithms (BFS and its variants, PageRank, SSSP) using our framework and verify their correctness. | Both   |
| Apr 22 - Apr 24 | Implement frontier-based optimizations (push vs. pull strategies).                                               | Noli   |
| Apr 24 - Apr 26 | Run kernel & framework performance benchmarks, analyze kernel timings, memory bandwidth stats. Compare with existing kernels and frameworks. | Devi   |
| Apr 26 - Apr 27 | Polish documentation, and write up results obtained from testing.                                               | Both   |
| Apr 27 - Apr 28 | Visuals, demo setup, final write-up, and submission.                                                            | Both   |

## Deliverables Progress Review

### Achieved

* **Core GraphBLAS Operations on GPU**
  * Implement SpMV operations with support for different semirings
  * Support for element-wise operations (EWiseAdd, EWiseMult)
  * Implement basic masking functionality for selective updates
  * Persistent Loop Kernel
* Efficient sparse matrix representation
* Matrix to CSR sparse matrix conversion

### In Progress

* **Algorithm Implementations using our framework**
  * Implement BFS (and its variants) & PageRank
  * Benchmark against other frameworks

* **Frontier-based optimization**

* **Benchmarking & Analysis**
  * Analysis of execution characteristics on different graph types
  * Detailed performance comparison against CPU-BLAS kernels

### Hope to Achieve

* Single-Source Shortest Path (SSSP) implementation using min-plus semiring
* Comparison against more specialized GPU graph frameworks like Gunrock

## Poster Session Plan

* Speedup comparison graphs
  * Our implementation vs. CPU-BLAS implementation
  * Speedup factors across different graph sizes to show scaling behavior

* Kernel launch overhead reduction
  * A graph showing execution time with traditional per-iteration launches vs. our single kernel approach
  * A bar graph showing time spent in compute vs. time spent in copying buffers to device and kernel launch overhead

* A visualization of the operation DAG

* CUDA profiler description highlighting memory-bound behavior

* Performance comparisons of our different graph traversal kernels on graphs with different sparsity profiles


## Current Concerns

The biggest concern we have is that sparsity patterns in graphs vary dramatically. This makes our kernel efficiency difficult to generalize; it would be very challenging to optimize for all cases. Currently we are thinking about developing a runtime classification system that could detect the sparsity pattern and switch between our different kernel implementations to maintain performance across different graph structures.

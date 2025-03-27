---
title: "Project Proposal"
date: 2025-03-26T20:20:46-04:00
draft: false
---

An overview of our 15-418 Final Project

<!--more-->

# GPU-Accelerated GraphBLAS

## Summary

We will implement a GPU-accelerated subset of the GraphBLAS standard to express graph algorithms using sparse matrix operations over various semirings. Our implementation will optimize critical matrix operations (particularly SpMV and SpMM) on NVIDIA GPUs through custom GPU kernels. The key focus areas include effectively handling irregular workloads inherent to graph processing and reducing kernel launch overhead common in iterative graph algorithms by staging computations within a single main GPU loop kernel.

## Background

Traditional graph algorithms such as  Breadth-First Search (BFS), Single-Source Shortest Path (SSSP), and PageRank are typically implemented using node and edge-centric approaches, making them intuitive but increasingly complex to parallelize as algorithms grow more complex. The GraphBLAS standard offers an alternative perspective, representing graph algorithms via sparse linear algebra operations over semirings. This linear algebraic approach leverages established mathematical structures to simplify algorithm design, which can be especially useful in parallel computing contexts.
Graphs encountered in practice typically end up sparse, mainly because real-world interactions themselves are usually limited and selective. GraphBLAS focuses on interpreting graphs as sparse matrices, enabling the use of their CPU optimized matrix operations like Sparse Matrix-Vector Multiplication (SpMV) and Sparse Matrix-Matrix Multiplication (SpMM).  Semirings (algebraic structures that redefine how matrix operations combine values) further enhance flexibility, allowing us to have tailored operations suited to diverse algorithmic needs, such as logical OR-AND for graph connectivity problems or MIN-PLUS for shortest-path calculations. Our motivation to explore a GPU-accelerated framework stems from the fact that matrix operations naturally align with GPU architectures, which excel at executing uniform instructions in parallel over large datasets. That being said, adopting GPUs for sparse graph processing introduces many challenges due to irregular memory access patterns and work distribution. The main focus of our project will be on finding effective strategies in overcoming these challenges.

## The Challenge

### Irregular Memory Access Patterns:

* Graph data is inherently sparse and irregular, leading to unpredictable memory access patterns that can reduce GPU performance.

* When performing sparse matrix operations, threads accessing neighboring vertices may need to fetch data from widely separated memory locations, leading to uncoalesced memory accesses. Additionally matrix vector multiplication is memory bound, not computation bound.

### Workload Imbalance:

* Real-world graphs often have “hot-spots” creating extreme imbalances in workload distribution.

* High-degree vertices require significantly more computation than low-degree vertices, leading to thread divergence and workload imbalance.
We must come up with our own custom work distribution strategy that will maximize hardware utilization.  

### Kernel Launch Overhead:

* Graph algorithms are typically iterative. This typically means requiring multiple kernel launches on GPUs.

* Each kernel launch introduces overhead that can dominate execution time, especially for high-diameter graphs that require many iterations.

### Dynamic Frontier:

* During algorithm execution, the frontier can change dramatically in density.

* We must change our processing strategy depending on the density of our frontier (e.g., sparse frontiers benefit from push-based approaches while dense frontiers benefit from pull-based approaches).

### Debugging

* It is hard to validate correctness of graph algorithms on large graphs so we may need to debug on smaller test cases or come up with some new way of validating our implementation.

## Resources

### Hardware

We are expecting to develop on NVIDIA GPUs (RTX 2080 Ti or newer). We do not require any extra resources beyond what is already available to us. That being said, access to higher-end NVIDIA GPUs with more memory would be appreciated for testing larger graphs, but it is not strictly necessary for our core implementation/proof of concept.

### Software and Libraries

We will be using the CUDA Toolkit for GPU programming and the cuSPARSE library as a baseline for sparse matrix operations

### Starting Point

We will be starting from scratch.

### Key References:

* Kepner, J., Aaltonen, P., Bader, D., Buluç, A., Franchetti, F., Gilbert, J., Hutchison, D., Kumar, M., Lumsdaine, A., Meyerhenke, H., McMillan, S., Moreira, J., Owens, J. D., Yang, C., Zalewski, M., & Mattson, T. (2016). Mathematical foundations of the GraphBLAS. arXiv preprint arXiv:1606.05790. https://doi.org/10.48550/arXiv.1606.05790

* Yang, C., Buluç, A., & Owens, J. D. (2018). Implementing push-pull efficiently in GraphBLAS. Proceedings of the International Conference on Parallel Processing (ICPP).

* Yang, C., Buluç, A., & Owens, J. D. (2019). GraphBLAST: A high-performance linear algebra-based graph framework on the GPU. arXiv preprint arXiv:1908.01407.

* The GraphBLAS User Guide & C API Specification (https://github.com/DrTimothyAldenDavis/GraphBLAS/blob/stable/Doc/GraphBLAS_UserGuide.pdf)

* Hwang, C., Park, K., Shu, R., Qu, X., Cheng, P., & Xiong, Y. (2023). ARK: GPU-driven code execution for distributed deep learning. In Proceedings of the 20th USENIX Symposium on Networked Systems Design and Implementation (NSDI). USENIX Association.

## Goals and Deliverables

### Plan to Achieve

#### Core GraphBLAS Operations on GPU:

* Implement SpMV and SpMM operations with support for different semirings

* Support for element-wise operations (EWiseAdd, EWiseMult)

* Implement basic masking functionality for selective updates

#### Algorithm Implementations:

* Implement and validate at least two graph algorithms (e.g. BFS & PageRank) using our framework.

#### Performance Optimizations:

* Adaptive kernel implementation that stays on the GPU across multiple iterations.

* Dynamic switching between push and pull approaches based on frontier density.

* Load balancing strategies for handling irregular workloads.

* Memory-efficient representations of sparse matrices.

#### Benchmarking and Analysis:

* Detailed performance comparison against CPU-BLAS kernels.

* Analysis of execution characteristics on different graph types (scale-free vs. random).

* Identification of performance bottlenecks and optimization opportunities

* Measurement of speedup relative to our previous approaches

#### Documentation

* Clear documentation of our implementation approach and design choices.

* We also expect to have a clear and easy to understand API.

### Hope to Achieve

### Additional Algorithms

* Single-Source Shortest Path (SSSP) implementation using min-plus semiring

### Extended Evaluation

* Comparison against more specialized GPU graph frameworks like Gunrock

## PLATFORM CHOICE

We are implementing our project as a C++ library with CUDA kernels. We want to use CUDA's mature ecosystem which includes libraries like cuSPARSE as building blocks. Additionally we already have access and experience with CUDA devices.

## SCHEDULE

### Week 1 (March 26 - March 29)

* Set up project repo and dependencies

* Review GraphBLAS specification and existing implementations

* Design data structures for sparse matrix representations

### Week 2 (March 30 - April 5)

* Implement core library kernels (e.g., sparse matrix kernels with semiring/masking support).

* Add tests for correctness against cuSPARSE kernels

* Create performance benchmarking infrastructure

* Establish baseline measurements with serial CPU implementation

### Week 3 (April 6 - April 12)

* Design a long-running GPU kernel that eliminates per-iteration launch overhead for iterative graph algorithms

* Optimize our sparse matrix kernels with proper load balancing

* Implement frontier-based optimizations (push vs. pull strategies)

* Complete BFS implementation with all optimizations

* Begin implementation of PageRank algorithm

* Perform additional performance analysis after making our optimizations

* Prepare milestone report (due April 15)

### Week 4 (April 13 - April 19)

* Complete PageRank implementation with iterative convergence

* Expand testing to larger and more diverse graph datasets

* Complete Milestone Report (Due April 15th)

### Week 5 (April 20 - April 26)

* Conduct detailed performance analysis and comparison with previous implementations as well as CPU baseline.

* Optimize remaining performance bottlenecks

* Prepare final report and documentation

* Create visualizations for poster presentation

* Complete any stretch goals if ahead of schedule

### Final Week (April 27 - April 29)

* Present project at poster session

* Final submission of code and documentation

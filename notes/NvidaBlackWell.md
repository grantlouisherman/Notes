# NVIDA Blackwell

## GLossary of terms

### What is a tensor core?
Specialized hardware units in NVIDIA GPUs designed to accelerate matrix math, particularly for deep learning (AI) and high-performance computing (HPC) by efficiently performing large matrix multiplications (A x B + C) much faster than general-purpose CUDA cores, using mixed-precision (like FP16 inputs to FP32 outputs) to boost speed without significant accuracy loss, and evolving to support more data types like INT8/INT4. They are crucial for training neural networks, enabling massive performance gains in AI workloads by handling core matrix operations rapidly.


### What is a CUDA core?
A fundamental parallel processing unit within an NVIDIA GPU, essentially a tiny arithmetic logic unit (ALU) that executes instructions for graphics rendering, AI, and high-performance computing tasks, working in massive groups inside Streaming Multiprocessors (SMs) to handle thousands of threads simultaneously, making GPUs powerful for parallel workloads.


### What is FP16/32?
FP16/32 (Half-Precision Floating-Point) is a 16-bit data format, using 1 bit for sign, 5 for exponent, and 10 for mantissa.
FP32 (Single-Precision Floating-Point) is a 32-bit data format, using 1 bit for sign, 8 for exponent, and 23 for mantissa.

## What is a mantissa?
[ Sign | Exponent | Mantissa ]
Mantissa represents the numbers after the decimal point in a floating-point number, and is used to represent fractional values in a floating-point number.
> Note the part to the left of the decimal point is callled **characteristic**

## What is a ALU?
ALU (Arithmetic Logic Unit) is a fundamental component of a CPU that performs arithmetic and logical operations on binary data

### What is an Accelerator
Specialized hardware (like NPUs, TPUs, GPUs, ASICs) designed for the heavy math of AI/ML.

## What is inference
The process where a pre-trained model uses its learned knowledge to make predictions, decisions, or generate outputs from new, unseen data, essentially putting its training into practice. I

## What is Tensor Memory (TMEM) Subsystem
a specialized, high-bandwidth on-chip memory introduced in NVIDIA's Blackwell architecture GPUs, such as the B200 series. Its primary purpose is to serve as dedicated, high-speed storage for the inputs and intermediate results of Tensor Core operations, reducing reliance on general-purpose registers and shared memory (SMEM) for matrix computations.


## Notes
- Growing demands have exposed several limitations of current GPU architectures, particularly within their memory hierarchies, precision flexibility, and latency-sensitive
task scheduling
- Innovation for accelerators is has become essential for advancing both throughput-optimized training and time-critical inference workloads
- The introduction of the Tensor Memory (TMEM) subsystem as a dedicated on-chip memory for tensor data movement reduces reliance on shared memory (SMEM) and per SM register files (RF) during matrix-intensive operations.
- The B200 GPU signifies a decisive progression in architectural philosophy. Previously, GPU generations from Tesla to Hopper focused on maximizing floating-point operations per
second (FLOPS) for large-scale model training.
- In contrast, Blackwell emphasizes post-training and inference efficiency, adopting transformational changes in both memory and compute organization.
- One B200 GPU includes a **dual-die configuration** where two GPU dies comprise 208 billion transistors, feature 148 SMs spread across eight GPCs, provide four L2 cache partitions (double those in Hopper), and include eight **HBM3e memory stacks**
- NVIDIA **High-Bandwidth Interface (NV-HBI)** providing a coherent and single device to software, with unified 192 GB HBM3e memory space.
- Within each Streaming Multiprocessor, Blackwell introduces fifth-generation Tensor Cores that break from the **warp-synchronous paradigm**
haracterizing earlier architectures
- Previous generations enforced that all 32 threads within a **warp synchronize** before executing **matrix multiply–accumulate (MMA)** operations via the mma.sync or wgmma instructions
- This **lock-step model** reduced scheduling flexibility and created idle cycles, especially for dependency chains of varying lengths
- Blackwell replaces warp-synchronous MMA with tcgen05.mma, a **single-thread instruction**. Now, each thread independently issues MMA operations, removing warp-level synchronization and enabling true per-thread scheduling for tensor operations.
- Tensor Memory (TMEM), Per SM, the TMEM provides memory access to and from tensor cores. Allocation, data movement, and deallocation are explicitly managed in software via the tcgen PTX set of instructions, giving compiler toolchains precise control over tile locality and traffic pattern
- Blackwell’s Tensor Cores introduce native 4-bit and 6-bit floating-point precision (FP4 and FP6) for quantized inference, further improving memory and computational efficiency for AI workloads
- Architectural innovations extend to the thread-block level, with **CTA pair execution: two Cooperative Thread Arrays (CTAs)** with adjacent ranks share operands, reducing redundant data movement. Each CTA pair maps to a TPC and leverages a dedicated intraTPC communication network for efficient operand sharing
- Blackwell’s Tensor Cores provide native support for convolution operators with weight-stationary dataflows that use a collector buffer to cache and reuse matrix B (weight tensor) operands. Hence, optimizing for convolutional kernels that benefit from operand locality

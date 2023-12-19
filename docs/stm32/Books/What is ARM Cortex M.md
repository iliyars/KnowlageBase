The Instruction Set Architecture (ISA) in the Cortex-M processors is called Thumb. ISA is based on Thumb-2 Technology wich supports a mixture of 16-bit and 32 bit instruction.

The Cortex-M3 and Cortex-M4 processors have:
- Three-stage pipeline design
- Harvard bus architecture with unified memory space: instructions and data use the same address space
- 32-bit addressing, supporting 4 GB of memory space
- Multiply Accumulate (MAC) and sature instructions
- Single Instreuction Multiply Data (SIMD) operations
- Instructions for system control, OS support etc.
- Saturating arithmetic instructions
- OPtional floating point instructions (single precision)
- On-chip bus interfaces based on ARM AMBA (Advanced Microcontroller Bus Architecture) Technolodgy, wich allowed pipelined bus operations for higher throughput

### The Cortex-M processor family
The Cortex-M3 and M4 processors are based on ARMv7-M architecture.
The Cortex-M0, Cortex-M0+ and the Cortex-M1 processors are based on ARMv6-M, which has a smaller instruction set.

The Cortex-M1 processor is designed specifically for FPGA applications. Is has Tightly Coupled Memory (TCM) features that can be implemented using memories inside the FPGA
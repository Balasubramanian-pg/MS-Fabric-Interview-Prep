# What Is Fast Copy

Canonical documentation for What Is Fast Copy. This document defines concepts, terminology, and standard usage.

## Purpose
The concept of "Fast Copy" addresses the inherent inefficiencies in traditional data duplication processes. In standard computing environments, copying data often involves redundant cycles of reading data into intermediate buffers, context switching between user and kernel spaces, and high CPU utilization. 

Fast Copy exists to minimize the latency and resource overhead associated with data movement. It aims to achieve near-instantaneous or hardware-optimized data replication by leveraging advanced file system features, direct memory access, or metadata manipulation rather than traditional bit-by-bit physical duplication.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Theoretical mechanisms of accelerated data transfer (e.g., Zero-copy, Reflinks).
* Architectural strategies for reducing I/O overhead.
* Logical vs. Physical data duplication concepts.
* Resource management during high-volume data movement.

**Out of scope:**
* Specific vendor software manuals (e.g., TeraCopy, Robocopy, or the "FastCopy" Windows utility).
* Programming language-specific syntax for `memcpy` or similar functions.
* Hardware-specific driver installation guides.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Zero-Copy** | An operation where the CPU does not perform the task of copying data from one memory area to another, typically bypassing the user-space/kernel-space boundary. |
| **Reflink (Reference Link)** | A metadata-level copy where multiple file entries point to the same physical data blocks on a disk until one is modified. |
| **DMA (Direct Memory Access)** | A feature of computer systems that allows certain hardware subsystems to access main system memory independently of the central processing unit. |
| **Copy-on-Write (CoW)** | An optimization strategy where the system defers the actual physical copy of data until a modification is attempted on one of the copies. |
| **I/O Bound** | A state where the speed of a task is limited by the throughput or latency of the input/output subsystem rather than the CPU. |
| **Sparse File** | A type of computer file that attempts to use file system space more efficiently when the file itself is mostly empty. |

## Core Concepts

### 1. Metadata-Based Duplication
In many "Fast Copy" scenarios, the system does not move actual data blocks. Instead, it updates the file system's index or inode table. By creating a new pointer to existing data blocks, the copy appears instantaneous to the user, regardless of the file size.

### 2. Buffer Management
Traditional copying involves reading from a source into a buffer and writing from that buffer to a destination. Fast Copy optimizes this by increasing buffer sizes to match hardware cache lines or by using "scatter-gather" I/O to handle non-contiguous data blocks efficiently.

### 3. Asynchronous Execution
Fast Copy operations often decouple the request for a copy from the completion of the physical write. This allows the calling process to continue while the storage controller or kernel manages the data transfer in the background.

### 4. Kernel-Space Optimization
By performing the copy entirely within the kernel space (using system calls like `sendfile` or `copy_file_range`), the system avoids the "context switching" overhead of moving data back and forth to a user-level application.

## Standard Model
The standard model for Fast Copy follows a tiered approach to optimization:

1.  **Request Validation:** The system determines if the source and destination reside on the same logical volume or hardware.
2.  **Mechanism Selection:** 
    *   If on the same volume: Use **Reflinks/CoW** (Metadata only).
    *   If on different volumes: Use **DMA/Zero-Copy** (Hardware acceleration).
    *   If over a network: Use **RDMA (Remote Direct Memory Access)**.
3.  **Execution:** The data is mapped. If physical movement is required, it is performed in large, aligned blocks to maximize throughput.
4.  **Verification:** Integrity checks (checksums) are performed asynchronously to ensure data fidelity without blocking the primary I/O path.

## Common Patterns

*   **Block-Level Cloning:** Used in virtualization and snapshots where entire disk images are "copied" by creating a new set of pointers to the original blocks.
*   **Parallelism:** Distributing a large copy task across multiple threads or I/O channels to saturate the available bandwidth of the storage medium (e.g., NVMe).
*   **Direct I/O:** Bypassing the operating system's page cache to prevent "cache pollution," ensuring that the copy operation doesn't slow down other active applications.

## Anti-Patterns

*   **Small Buffer Iteration:** Using small, fixed-size buffers (e.g., 4KB) for large file transfers, which leads to excessive system calls and CPU overhead.
*   **Synchronous Blocking:** Forcing the entire system to wait for a physical write to complete before acknowledging the copy command.
*   **Redundant Verification:** Performing byte-by-byte comparison immediately after a copy on high-reliability media where metadata checksums are already present.
*   **Ignoring Alignment:** Starting read/write operations at offsets that do not align with the physical sector or page size of the storage media.

## Edge Cases

*   **Cross-Filesystem Boundaries:** When a "Fast Copy" is requested between two different file systems (e.g., NTFS to EXT4), metadata-only tricks (Reflinks) fail, and the system must fall back to optimized physical bit-copying.
*   **Sparse File Expansion:** Copying a sparse file using a non-aware "Fast Copy" method may result in the destination file occupying the full logical size on disk, losing the space-saving benefits.
*   **In-Flight Modifications:** If the source data is modified while a Fast Copy (specifically a non-CoW copy) is in progress, the destination may end up in a corrupted or inconsistent state.
*   **Hardware Buffer Saturation:** In extreme high-speed copies, the storage controller's cache may saturate, leading to a "cliff" where performance drops suddenly to the raw NAND/Platter speed.

## Related Topics

*   **Atomic Operations:** Ensuring a copy is "all or nothing."
*   **Data Deduplication:** The process of eliminating redundant copies of data at rest.
*   **Storage Area Networks (SAN):** Offloading copy commands to the fabric via protocols like VAAI (vStorage APIs for Array Integration).
*   **Memory Mapping (mmap):** A method of I/O that maps files directly into a process's address space.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |
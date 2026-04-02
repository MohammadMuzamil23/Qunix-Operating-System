# Qunix Operating System Overview

## Introduction

Qunix is a modern, Unix-like operating system kernel written entirely in Rust for the x86-64 architecture. It aims to provide a complete, production-ready OS with full POSIX compatibility, advanced features like memory tagging and namespaces, and a rich userland environment.

## Key Features

### Core Kernel Features
- **Full Process Model**: Complete fork/exec/wait/exit semantics with proper signal handling
- **Virtual File System (VFS)**: Layered filesystem abstraction supporting ext4, FAT32, tmpfs, devfs, and procfs
- **Memory Management**: Demand-paged virtual memory with Copy-on-Write (CoW) fork, mmap/munmap, and transparent huge pages
- **Scheduler**: Priority-based preemptive scheduler with multi-CPU support
- **Signals**: Full POSIX signal implementation with sigaction, sigreturn, and signal queues
- **TTY Subsystem**: Line discipline with canonical/raw modes, job control (Ctrl+C/Z), and termios
- **IPC Mechanisms**: Pipes, shared memory, message queues, semaphores, and futexes
- **Networking**: TCP/UDP socket support with IP protocol stack
- **Security**: Namespaces, memory tagging, seccomp, and MAC policies
- **Plugins**: Loadable kernel modules for extensibility

### Userland
- **70+ Utilities**: Complete set of coreutils, shell, and system tools
- **Shell (qsh)**: Modern interactive shell with syntax highlighting, history, and job control
- **Init System**: System initialization and service management
- **Filesystem Tools**: Support for multiple filesystem formats

### Advanced Features
- **ABI Compatibility**: Linux syscall compatibility layer
- **DRM/KMS**: Direct Rendering Manager for graphics
- **I/O Uring**: High-performance asynchronous I/O
- **Performance Monitoring**: Built-in profiling and tracing tools
- **RTOS Support**: Real-time scheduling capabilities

## Architecture

Qunix follows a monolithic kernel design with modular subsystems. The kernel is written in Rust with no_std, providing memory safety and modern language features while maintaining high performance.

### Kernel Subsystems
- **arch/x86_64**: CPU-specific code (GDT, IDT, paging, syscalls)
- **memory**: Physical/virtual memory management
- **process**: Process control blocks and lifecycle
- **sched**: CPU scheduling and context switching
- **syscall**: System call dispatch and handlers
- **vfs/fs**: Virtual and concrete filesystem implementations
- **drivers**: Hardware device drivers
- **net**: Network protocol stack
- **security**: Access control and isolation

### Boot Process
1. UEFI bootloader loads kernel and initramfs
2. Kernel initializes hardware and core subsystems
3. Filesystems are mounted
4. Userland init process starts
5. Shell and services launch

## Development Status

Qunix is under active development with the following milestones:
- [x] Basic kernel boot and serial output
- [x] Memory management and paging
- [x] Process creation and scheduling
- [x] Filesystem support
- [x] Userland utilities
- [x] Networking stack
- [x] Plugin system
- [ ] Full POSIX compliance testing
- [ ] SMP optimization
- [ ] Advanced security features

## Getting Started

See [Building Qunix](building.md) for compilation instructions and [User Guide](user_guide.md) for usage information.
# Qunix Architecture

## Kernel Architecture

Qunix implements a monolithic kernel design where all core OS services run in kernel space. This provides high performance and tight integration while leveraging Rust's memory safety guarantees.

### Core Principles
- **Memory Safety**: All kernel code is written in Rust with strict ownership and borrowing rules
- **Modularity**: Subsystems are organized into separate modules with clear interfaces
- **Performance**: Zero-cost abstractions and efficient data structures
- **Compatibility**: POSIX-compliant APIs and Linux ABI compatibility

## Subsystem Overview

### Architecture Layer (arch/x86_64/)
Handles CPU-specific functionality:
- **GDT/IDT**: Global and Interrupt Descriptor Tables for segmentation and interrupts
- **Paging**: 4-level page tables with support for huge pages
- **Syscall Entry**: Fast syscall instruction handling
- **SMP**: Symmetric multiprocessing initialization and CPU management

### Memory Management (memory/)
Implements virtual memory:
- **Physical Frame Allocator**: Tracks free RAM frames
- **Virtual Memory Manager**: Page table manipulation and address spaces
- **Slab Allocator**: Kernel object allocation
- **CoW Fork**: Copy-on-write process cloning
- **Transparent Huge Pages**: Automatic large page promotion

### Process Management (process/)
Process lifecycle and control:
- **Process Control Block (PCB)**: Process state and metadata
- **Fork/Exec/Exit**: Process creation and termination
- **Process Groups/Sessions**: Job control support
- **Credentials**: UID/GID management

### Scheduler (sched/)
CPU time management:
- **Priority Queues**: Multiple run queues by priority
- **Preemptive Scheduling**: Timer interrupts for context switches
- **Multi-CPU Support**: Per-CPU run queues and load balancing
- **Real-time Classes**: RT scheduling policies

### System Calls (syscall/)
User-kernel interface:
- **Dispatch Table**: Fast lookup of syscall handlers
- **Argument Validation**: Safe parameter checking
- **Return Values**: Proper errno handling

### Virtual File System (vfs/)
Filesystem abstraction:
- **Inode/Dentry Cache**: Metadata caching
- **Mount Points**: Filesystem mounting
- **Path Resolution**: Name lookup and traversal
- **Permissions**: Access control checking

### Concrete Filesystems (fs/)
Storage implementations:
- **ext4**: Journaled filesystem with extents
- **FAT32**: Legacy filesystem support
- **tmpfs**: RAM-based temporary storage
- **devfs**: Device node management
- **procfs**: Process information exposure

### Device Drivers (drivers/)
Hardware interface:
- **PCI**: Device enumeration and configuration
- **NVMe**: High-speed storage
- **Serial/VGA**: Console output
- **Keyboard**: Input handling
- **Network**: Ethernet device support

### Networking (net/)
Protocol stack:
- **IP**: Internet Protocol v4/v6
- **TCP/UDP**: Transport layer
- **Sockets**: BSD socket API
- **Routing**: Packet forwarding

### IPC (ipc/)
Inter-process communication:
- **Pipes**: Anonymous and named pipes
- **Shared Memory**: shmget/shmat
- **Message Queues**: System V IPC
- **Semaphores**: Synchronization primitives
- **Futex**: Fast userspace mutexes

### Security (security/)
Access control:
- **Namespaces**: Process isolation
- **Memory Tagging**: Hardware-assisted bounds checking
- **Seccomp**: System call filtering
- **MAC Policies**: Mandatory access control

### Signals (signal/)
Asynchronous notifications:
- **Signal Delivery**: Queue and dispatch
- **Sigreturn**: Signal frame handling
- **Signal Masks**: Blocking and pending sets

### TTY (tty/)
Terminal handling:
- **Line Discipline**: Input processing
- **Termios**: Terminal attributes
- **Job Control**: Process groups and sessions

## Userland Architecture

### Init System
- **PID 1**: System initialization
- **Mounting**: Essential filesystems
- **Service Launch**: Starting system services

### Shell (qsh)
- **Command Parsing**: Syntax analysis
- **Job Control**: Background processes
- **History**: Command persistence
- **Completion**: Context-aware suggestions

### Utilities
- **Coreutils**: File and text manipulation
- **System Tools**: Process management, networking
- **Development**: Compilers, debuggers

## Plugin System

Qunix supports loadable kernel modules called "plugins":
- **Hot Loading**: Runtime module insertion/removal
- **Hooks**: Integration points in kernel subsystems
- **Configuration**: Plugin-specific settings
- **Examples**: Performance monitoring, syscall logging

## Boot Process

1. **UEFI Bootloader**: Loads kernel and initramfs
2. **Kernel Entry**: `kernel_main()` function
3. **Hardware Init**: GDT, IDT, memory, timers
4. **Subsystem Init**: Drivers, filesystems, networking
5. **Userland Launch**: Start init process
6. **System Ready**: Shell available for user interaction

## Data Structures

### Key Kernel Objects
- **Process**: PCB with address space, file descriptors, signals
- **Thread**: Execution context within a process
- **File**: VFS inode with position and flags
- **Socket**: Network endpoint with protocol state
- **Inode**: Filesystem metadata
- **Dentry**: Directory entry cache

### Synchronization
- **Spinlocks**: Low-level mutual exclusion
- **Mutexes**: Sleeping locks with priority inheritance
- **RW Locks**: Reader-writer synchronization
- **Semaphores**: Counting semaphores
- **Futexes**: Userspace-assisted locking

## Memory Layout

### Kernel Space
- **0xFFFF_8000_0000_0000 - 0xFFFF_FFFF_FFFF_FFFF**: Kernel code and data
- **Direct Map**: Physical memory access
- **VMALLOC**: Dynamic kernel allocations

### User Space
- **0x0000_0000_0000_0000 - 0x0000_7FFF_FFFF_FFFF**: User applications
- **Stack**: Grows downward from high addresses
- **Heap**: Managed by brk/mmap
- **Shared Libraries**: Loaded in user space

## Security Model

- **Ring 0**: Kernel privileged execution
- **Ring 3**: User unprivileged execution
- **Syscall Gate**: Controlled kernel entry
- **Capabilities**: Fine-grained privilege control
- **Namespaces**: Container isolation
- **Seccomp**: System call sandboxing
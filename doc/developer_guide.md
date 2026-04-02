# Qunix Developer Guide

## Contributing

### Development Setup
1. Fork the repository
2. Clone your fork: `git clone https://github.com/yourusername/Qunix-Operating-System.git`
3. Set up development environment: `./SETUP.sh`
4. Create a feature branch: `git checkout -b feature-name`

### Code Style
- Follow Rust standard formatting: `cargo fmt`
- Run clippy: `cargo clippy`
- Write tests for new functionality
- Document public APIs

### Commit Guidelines
- Use clear, descriptive commit messages
- Keep commits focused on single changes
- Reference issues in commit messages

## Kernel Development

### Project Structure
```
kernel/
├── src/
│   ├── main.rs              # Kernel entry point
│   ├── arch/x86_64/         # Architecture-specific code
│   ├── memory/              # Memory management
│   ├── process/             # Process management
│   ├── sched/               # Scheduler
│   ├── syscall/             # System calls
│   ├── vfs/                 # Virtual filesystem
│   ├── fs/                  # Filesystem implementations
│   ├── drivers/             # Device drivers
│   └── ...
├── Cargo.toml               # Dependencies
├── x86_64-qunix.json        # Target specification
└── kernel.ld                # Linker script
```

### Adding a System Call

1. **Define the syscall number** in `syscall/mod.rs`
2. **Add handler function** in `syscall/handlers.rs`
3. **Register in dispatch table** in `syscall/mod.rs`
4. **Add userland wrapper** in `libsys`

Example:
```rust
// kernel/src/syscall/handlers.rs
pub fn sys_mysyscall(&mut self, arg1: usize, arg2: usize) -> Result<usize, Errno> {
    // Implementation
    Ok(0)
}

// kernel/src/syscall/mod.rs
const MY_SYSCALL: usize = 400;
syscall!(MY_SYSCALL, mysyscall);
```

### Writing a Device Driver

1. **Create driver module** in `drivers/`
2. **Implement driver trait**:
```rust
pub trait Driver {
    fn init(&self) -> Result<(), ()>;
    fn name(&self) -> &str;
}
```
3. **Register with driver manager** in `drivers/mod.rs`

### Memory Management

#### Allocating Kernel Memory
```rust
use alloc::boxed::Box;
use alloc::vec::Vec;

// Box for single objects
let obj = Box::new(MyStruct::new());

// Vec for dynamic arrays
let mut vec = Vec::new();
vec.push(item);
```

#### Physical Memory
```rust
use crate::memory::phys;

// Allocate frame
let frame = phys::alloc_frame()?;

// Free frame
phys::free_frame(frame);
```

#### Virtual Memory
```rust
use crate::memory::vmm;

// Map page
vmm::map_page(vaddr, paddr, flags)?;

// Unmap page
vmm::unmap_page(vaddr)?;
```

### Process Management

#### Creating a Process
```rust
use crate::process::{Process, ProcessState};

let mut proc = Process::new()?;
proc.state = ProcessState::Runnable;
// Configure address space, file descriptors, etc.
```

#### Context Switching
```rust
use crate::sched::context_switch;

unsafe {
    context_switch(&mut current->context, &next->context);
}
```

## Userland Development

### Project Structure
```
userland/
├── Cargo.toml               # Workspace configuration
├── user.ld                  # Linker script
├── x86_64-qunix-user.json   # Target spec
└── [program]/               # Individual programs
    ├── Cargo.toml
    └── src/main.rs
```

### Creating a New Utility

1. **Create directory**: `mkdir userland/myutil`
2. **Add Cargo.toml**:
```toml
[package]
name = "myutil"
version = "0.1.0"

[dependencies]
libsys = { path = "../libsys" }
```
3. **Implement main.rs**:
```rust
#![no_std]
#![no_main]

use libsys::*;

#[no_mangle]
pub extern "C" fn _start() -> ! {
    // Program logic
    exit(0);
}
```

### Using libsys

libsys provides Rust wrappers for system calls:

```rust
use libsys::*;

// File operations
let fd = open(b"/etc/passwd\0", O_RDONLY, 0);
let mut buf = [0u8; 1024];
let n = read(fd, &mut buf);
close(fd);

// Process management
let pid = fork();
if pid == 0 {
    // Child process
    execve(b"/bin/ls\0", &[], &[]);
} else {
    // Parent process
    waitpid(pid, &mut status, 0);
}
```

## Plugin Development

### Creating a Plugin

1. **Create plugin directory**: `mkdir plugins/myplugin`
2. **Add configuration**: `plugins/myplugin/main.conf`
3. **Implement plugin**: `plugins/myplugin/plug/main.rs`

Example plugin:
```rust
use crate::plugins::{Plugin, PluginMeta, PluginEntry};

pub struct MyPlugin;

impl Plugin for MyPlugin {
    fn init(&self) {
        klog!("My plugin initialized");
    }

    fn deinit(&self) {
        klog!("My plugin deinitialized");
    }

    fn pre_syscall(&self, ctx: &SyscallCtx) {
        // Hook logic
    }
}

plugin_entry!(MyPlugin);
```

### Plugin Hooks
- `init()`: Called when plugin is loaded
- `deinit()`: Called when plugin is unloaded
- `pre_syscall()`: Before syscall execution
- `post_syscall()`: After syscall execution
- `scheduler_tick()`: On scheduler timer tick

## Testing

### Unit Tests
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_my_function() {
        assert_eq!(my_function(), expected);
    }
}
```

### Integration Tests
Use the `scripts/run_abi_tests.sh` for ABI compatibility testing.

### Manual Testing
```bash
# Build and run
./build.sh run

# Test in QEMU
# Use shell commands to verify functionality
```

## Debugging

### Kernel Debugging
- Use `klog!` macro for debug output
- Check serial console output
- Use GDB with QEMU: `./run_qemu.sh --gdb`

### Userland Debugging
```bash
# Strace system calls
strace command

# Enable syscall logger plugin
pluginctl enable syscall_logger
```

### Common Debug Techniques
```rust
// Debug prints
klog!("Value: {}", value);

// Assertions
debug_assert!(condition);

// Panic with context
panic!("Something went wrong: {:?}", error);
```

## Performance Optimization

### Profiling
- Use `perf_monitor` plugin for basic profiling
- Enable syscall logger to identify bottlenecks
- Use `time` command for userland profiling

### Optimization Tips
- Use `#[inline]` for small functions
- Avoid allocations in hot paths
- Use appropriate data structures
- Profile-guided optimization for release builds

## Documentation

### Code Documentation
```rust
/// Brief description of function
///
/// # Arguments
/// * `arg1` - Description of arg1
/// * `arg2` - Description of arg2
///
/// # Returns
/// Description of return value
///
/// # Examples
/// ```
/// let result = my_function(1, 2);
/// ```
pub fn my_function(arg1: i32, arg2: i32) -> i32 {
    // Implementation
}
```

### API Documentation
- Document all public interfaces
- Include usage examples
- Explain error conditions

## Security Considerations

### Kernel Security
- Validate all inputs from userspace
- Use safe Rust practices
- Implement proper access controls
- Regular security audits

### Userland Security
- Drop privileges when possible
- Validate inputs
- Use safe system calls
- Follow principle of least privilege

## Release Process

1. Update version numbers
2. Run full test suite
3. Update documentation
4. Create release branch
5. Tag release
6. Build release artifacts
7. Publish release notes

## Getting Help

### Resources
- [Rust Documentation](https://doc.rust-lang.org/)
- [OSDev Wiki](https://wiki.osdev.org/)
- [Linux Kernel Documentation](https://kernel.org/doc/)
- Project issue tracker

### Communication
- GitHub Issues for bugs
- Pull Request discussions
- Email for security issues
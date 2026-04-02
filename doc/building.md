# Building Qunix

## Prerequisites

### System Requirements
- **OS**: Linux (Ubuntu 20.04+ recommended)
- **Architecture**: x86-64
- **RAM**: 4GB+ for compilation
- **Disk**: 10GB+ free space

### Dependencies
Install required tools:

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install -y \
    lld \
    mtools \
    xorriso \
    qemu-system-x86_64 \
    curl \
    build-essential

# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env

# Install nightly Rust with required components
rustup toolchain install nightly
rustup component add rust-src --toolchain nightly
```

Or use the provided setup script:
```bash
./SETUP.sh
```

## Build Process

### Quick Build
```bash
# Build kernel only
./build.sh kernel

# Build everything and create bootable ISO
./build.sh iso

# Build and run in QEMU
./build.sh run
```

### Build Scripts
- **`build.sh`**: Main build orchestration script
- **`build_kernel.sh`**: Kernel-specific compilation

### Build Targets
- **`kernel`**: Compile the kernel binary
- **`userland`**: Build all userland utilities
- **`iso`**: Create bootable ISO image
- **`run`**: Build and launch in QEMU

## Detailed Build Steps

### 1. Kernel Build
The kernel is built with a custom Rust target specification:

```bash
# Custom target for freestanding x86-64
rustup run nightly cargo build \
    --target x86_64-qunix.json \
    --release \
    --manifest-path kernel/Cargo.toml
```

Key build features:
- **no_std**: No standard library
- **build-std**: Custom core/alloc implementation
- **lld linker**: Fast linking
- **Custom target**: Freestanding environment

### 2. Userland Build
Userland programs are built for the custom user target:

```bash
# Build all userland utilities
rustup run nightly cargo build \
    --target x86_64-qunix-user.json \
    --release \
    --manifest-path userland/Cargo.toml \
    --bins
```

### 3. Bootloader
UEFI bootloader using rust-efi:

```bash
# Build bootloader
cargo build --release --manifest-path bootloader/Cargo.toml
```

### 4. ISO Creation
Final bootable image:

```bash
# Create FAT32 EFI partition
mtools -c mcopy ...

# Generate GRUB config
# Create ISO with xorriso
```

## Build Configuration

### Kernel Configuration
- **x86_64-qunix.json**: Target specification
- **kernel.ld**: Linker script
- **Cargo.toml**: Dependencies and features

### Userland Configuration
- **x86_64-qunix-user.json**: User target spec
- **user.ld**: User program linker script

### Build Options
Environment variables:
- **`QEMU_MEMORY`**: RAM size (default: 512MB)
- **`QEMU_CPUS`**: CPU count (default: 1)
- **`QEMU_DEBUG`**: Enable debug output

## Troubleshooting

### Common Issues

#### Rust Toolchain Problems
```bash
# Update rustup
rustup update

# Reinstall nightly
rustup toolchain uninstall nightly
rustup toolchain install nightly
rustup component add rust-src --toolchain nightly
```

#### Linker Errors
Ensure `lld` is installed:
```bash
sudo apt-get install lld
```

#### QEMU Issues
Install latest QEMU:
```bash
sudo apt-get install qemu-system-x86_64
```

### Build Logs
Check build output for errors:
```bash
./build.sh kernel 2>&1 | tee build.log
```

### Clean Build
```bash
# Clean all artifacts
cargo clean --manifest-path kernel/Cargo.toml
cargo clean --manifest-path userland/Cargo.toml
cargo clean --manifest-path bootloader/Cargo.toml

# Remove build directories
rm -rf target/
```

## Development Build

### Debug Build
```bash
# Build with debug symbols
./build.sh kernel --debug

# Run with GDB
./run_qemu.sh --gdb
```

### Incremental Builds
The build system supports incremental compilation for faster development cycles.

### Testing
```bash
# Run ABI tests
./scripts/run_abi_tests.sh

# Manual testing in QEMU
./build.sh run
```

## Cross-Compilation

Qunix can be cross-compiled on different architectures, though x86-64 host is recommended for QEMU testing.

## Build Artifacts

After successful build:
- **`kernel/target/x86_64-qunix/release/qunix`**: Kernel binary
- **`bootloader/target/release/bootloader.efi`**: UEFI bootloader
- **`userland/target/x86_64-qunix-user/release/`**: Userland binaries
- **`qunix.iso`**: Bootable ISO image

## Performance Optimization

### Release Builds
- **LTO**: Link-time optimization enabled
- **Optimizations**: Full Rust optimizations
- **Stripping**: Debug symbols removed

### Debug Builds
- **Debug Symbols**: Full debugging information
- **No Optimizations**: Faster compilation
- **Verbose Logging**: Additional kernel messages
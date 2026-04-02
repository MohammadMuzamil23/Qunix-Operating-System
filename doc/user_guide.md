# Qunix User Guide

## Getting Started

### Booting Qunix
After building, run QEMU:
```bash
./build.sh run
```

This will:
1. Launch QEMU with the Qunix ISO
2. Boot the kernel
3. Start the init system
4. Present a shell prompt

### First Login
Qunix boots directly to a root shell. No login required.

## Shell Usage

### qsh - Qunix Shell
Qunix includes a modern shell with advanced features:

```bash
# Interactive prompt
qunix:/root$ 

# Basic commands
ls
cd /bin
cat /etc/hostname
```

### Shell Features
- **Syntax Highlighting**: Color-coded commands and output
- **Auto-completion**: Tab completion for commands and paths
- **History**: Up and down arrows for command history
- **Job Control**: Ctrl+Z to suspend, `fg`/`bg` to manage jobs
- **Piping**: `|` operator for command pipelines
- **Redirection**: `>` and `<` for file I/O

### Basic Commands
```bash
# File operations
ls [directory]          # List files
cat file                # Display file contents
cp src dst              # Copy files
mv src dst              # Move/rename files
rm file                 # Delete files

# Directory operations
cd directory            # Change directory
pwd                     # Print working directory
mkdir directory         # Create directory
rmdir directory         # Remove directory

# Process management
ps                      # List processes
kill pid                # Terminate process
top                     # Process monitor

# System information
uname -a                # System information
df                      # Disk usage
free                    # Memory usage
uptime                  # System uptime
```

## Filesystem

### Directory Structure
```
/               # Root directory
├── bin/         # Essential user binaries
├── sbin/        # System administration binaries
├── etc/         # Configuration files
├── home/        # User home directories
├── root/        # Root user home
├── tmp/         # Temporary files
├── var/         # Variable data
├── usr/         # User programs
├── proc/        # Process information
├── sys/         # System information
├── dev/         # Device files
└── run/         # Runtime data
```

### Supported Filesystems
- **ext4**: Primary filesystem with journaling
- **tmpfs**: RAM-based temporary storage
- **devfs**: Device node filesystem
- **procfs**: Process information
- **FAT32**: External media support

### Mounting
```bash
# Mount a filesystem
mount /dev/sda1 /mnt

# Unmount
umount /mnt
```

## Process Management

### Running Programs
```bash
# Execute a program
/bin/ls

# Run in background
command &

# Job control
jobs                    # List jobs
fg %1                   # Foreground job 1
bg %1                   # Background job 1
```

### Signals
```bash
# Send signal to process
kill -TERM 123         # Graceful termination
kill -KILL 123         # Force kill
kill -STOP 123         # Stop process
kill -CONT 123         # Continue stopped process
```

## Networking

### Network Configuration
```bash
# Show network interfaces
ip addr

# Configure interface
ip addr add 192.168.1.100/24 dev eth0
ip link set eth0 up

# Set default route
ip route add default via 192.168.1.1
```

### Network Tools
```bash
# Test connectivity
ping 8.8.8.8

# DNS lookup
nslookup google.com

# Network statistics
netstat -tuln
```

## System Administration

### User Management
```bash
# Change user
su username

# Change password
passwd
```

### System Control
```bash
# Shutdown
shutdown now

# Reboot
reboot

# System logs
dmesg
```

### Plugin Management
```bash
# List plugins
pluginctl list

# Enable plugin
pluginctl enable perf_monitor

# Disable plugin
pluginctl disable perf_monitor
```

## Development

### Compiling Programs
Qunix supports Rust development:

```bash
# Create new project
cargo new myprogram

# Build
cargo build --target x86_64-qunix-user

# Run
./target/x86_64-qunix-user/debug/myprogram
```

### Debugging
```bash
# Kernel debug output
dmesg | tail

# Process debugging
strace command

# System call tracing
pluginctl enable syscall_logger
```

## Advanced Usage

### Kernel Modules
```bash
# Load module
insmod module.ko

# Remove module
rmmod module

# List modules
lsmod
```

### Device Management
```bash
# List devices
ls /dev/

# Create device node
mknod /dev/mydevice c 100 0
```

### Performance Monitoring
```bash
# CPU usage
top

# Memory usage
free -h

# Disk I/O
iostat

# Network I/O
ip -s link
```

## Troubleshooting

### Common Issues

#### System Hangs
- Check kernel logs: `dmesg`
- Kill unresponsive processes: `kill -9 PID`
- Restart system: `reboot`

#### Network Problems
- Check interface status: `ip link`
- Renew DHCP: `dhclient eth0`
- Check routes: `ip route`

#### Filesystem Issues
- Check disk space: `df -h`
- Repair filesystem: `fsck /dev/sda1`
- Remount: `mount -o remount /`

### Getting Help
```bash
# Manual pages
man command

# Command help
command --help

# System information
uname -a
cat /proc/version
```

## Customization

### Shell Configuration
Edit `~/.qshrc` for shell customization:
```bash
# Aliases
alias ll='ls -l'
alias la='ls -a'

# Environment variables
export PATH=$PATH:/usr/local/bin
export EDITOR=nano
```

### System Configuration
Key configuration files:
- `/etc/hostname`: System hostname
- `/etc/profile`: Global shell configuration
- `/etc/fstab`: Filesystem mount table

### Startup Scripts
Add services to `/etc/init.d/` for automatic startup.

## Security

### Best Practices
- Run as non-root when possible
- Use strong passwords
- Keep system updated
- Monitor logs regularly

### Access Control
```bash
# Change permissions
chmod 644 file
chown user:group file

# Setuid programs
chmod u+s program
```

## Performance Tuning

### Memory Management
```bash
# Clear page cache
echo 3 > /proc/sys/vm/drop_caches

# Adjust swappiness
echo 10 > /proc/sys/vm/swappiness
```

### CPU Scheduling
```bash
# Set process priority
nice -n 10 command
renice 10 PID
```

### I/O Optimization
```bash
# I/O scheduler
echo deadline > /sys/block/sda/queue/scheduler

# Read-ahead
blockdev --setra 256 /dev/sda
```
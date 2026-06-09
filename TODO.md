### TODO

This TODO list focuses on the OS features and implementations that are currently missing, incomplete, or stubbed out in the repository.

## Kernel / Syscall Support
- Implement `sys_fchdir` path reconstruction from an inode instead of returning success without updating CWD.
- Add hardlink support for `sys_link` and compatible filesystem link operations.
- Implement proper permission changes for `sys_chmod`, `sys_fchmod`, `sys_fchmodat`, `sys_chown`, `sys_fchown`, `sys_lchown`, `sys_fchownat`.
- Implement `sys_alarm` and the POSIX timer APIs (`setitimer`, `getitimer`, `timer_create`, `timer_settime`, `timer_gettime`, `timer_delete`).
- Add inotify support: `inotify_init1`, `inotify_add_watch`, `inotify_rm_watch`.
- Add `io_uring` syscall support: `io_uring_setup`, `io_uring_enter`, `io_uring_register`.
- Add `pidfd_open` support.
- Review and implement stubbed sandboxing / security syscalls such as `landlock_create_ruleset` if targeted.
- Implement kernel module loading support, or remove `init_module` and `delete_module` syscall stubs if not supported.
- Complete the VDSO setup by filling the VDSO page with a valid `clock_gettime` trampoline.
- Audit syscall dispatch for `sys_unimplemented` and add support or proper ENOSYS handling for missing syscalls.

## Filesystem / VFS
- Implement `fchdir` behavior to resolve directory file descriptors properly.
- Ensure hardlink semantics are supported correctly in VFS and underlying filesystem implementations.
- Validate FAT32 root inode handling and remove stub `SuperblockOps` methods that return `ENOSYS` where real behavior is expected.

## Device Drivers
- Implement AHCI SATA driver support under `kernel/src/drivers/pcie/mod.rs`.
- Complete block driver `total_blocks()` support for block devices.
- Finish DRM/KMS event integration by delivering vblank events to the FD event queue.
- Review hardware support limitations and document them clearly (AHCI, GPU, USB, block controllers).

## Security
- Complete memory tagging extensions (MTE) support or mark as intentionally unsupported in docs.
- Implement or stabilize seccomp filtering and MAC policy enforcement.
- Finish namespace support, especially PID and user namespaces.
- Harden the kernel with additional mitigations if planned (ASLR, stack canaries, CFI/CET).

## Userland / Utilities
- Finish incomplete `sed` features: `n`, `N`, `r`, `w`, and other script commands.
- Audit `qsh` and userland builtins for missing or partially implemented functionality.
- Ensure core utilities match their documented behavior and handle edge cases correctly.

## Documentation
- Update `README.md`, `doc/overview.md`, and other docs to match the actual implemented feature set.
- Document unimplemented or partially implemented features clearly as work-in-progress.
- Keep changelog and release notes aligned with code status.

## Quality / Testing
- Add POSIX compliance tests for filesystem, signals, timers, and syscall behavior.
- Add regression tests for newly implemented device drivers and security subsystems.
- Validate userland utility behavior against standard Unix expectations.

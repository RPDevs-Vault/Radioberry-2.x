# Raspberry Pi 32-bit / ARMv7 hard-float Linux

This target contains FTDI D2XX 1.4.35 for **32-bit ARMv7 hard-float** Linux
userspace with glibc, such as a compatible 32-bit Raspberry Pi OS installation
on an ARMv7-compatible processor.

For a 64-bit Raspberry Pi OS installation, use
[aarch64](../aarch64/README.md).
This ARMv7 library does not support ARMv6-only models such as the original
Raspberry Pi 1 and Pi Zero/Zero W. Those require a separate ARMv6 package.

On Raspberry Pi OS or Ubuntu, check the userspace architecture with:

```bash
dpkg --print-architecture
```

For this target, expect `armhf`. Also check that the processor supports ARMv7
instructions: the name `armhf` alone does not distinguish between all
Raspberry Pi OS variants and processor generations.

Source package: `libftd2xx-linux-arm-v7-hf-1.4.35.tgz`.
The shared library was verified as ELF32 / ARM, ARMv7 with a hard-float ABI.

`include/` contains the headers; `lib/` contains shared and static libraries.
See `README.pdf` for the original FTDI documentation and `SOURCE.txt` for
the archive checksum.

Build instructions: [BUILD-README.md](../../../../BUILD-README.md).
The Linux Makefile uses these local libraries and creates a distribution
directory for each architecture.

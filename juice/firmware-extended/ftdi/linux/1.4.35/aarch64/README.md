# Raspberry Pi 64-bit / ARM64 Linux

This is the **64-bit Raspberry Pi version** of FTDI D2XX 1.4.35.
`aarch64` is the architecture name for 64-bit ARM, also known as `arm64`.

Use these files for:

- Raspberry Pi running 64-bit Raspberry Pi OS.
- Raspberry Pi running 64-bit Ubuntu for ARM.
- Other compatible ARM64 Linux systems using glibc.

Both the application and Linux userspace must use 64-bit ARM.
A Raspberry Pi with a 64-bit processor running a 32-bit OS needs a 32-bit
library; see [armhf](../armhf/README.md).

On Raspberry Pi OS or Ubuntu, check the userspace architecture with:

```bash
dpkg --print-architecture
```

For this target, expect `arm64`. `uname -m` usually reports `aarch64`, but
it describes the kernel architecture and cannot by itself rule out a
32-bit userspace.

Source package: `libftd2xx-linux-arm-v8-1.4.35.tgz`.
The shared library was verified as ELF64 / AArch64.

`include/` contains the headers; `lib/` contains shared and static libraries.
See `README.pdf` for the original FTDI documentation and `SOURCE.txt` for
the archive checksum.

Build instructions: [BUILD-README.md](../../../../BUILD-README.md).
The Linux Makefile uses these local libraries and creates a distribution
directory for each architecture.

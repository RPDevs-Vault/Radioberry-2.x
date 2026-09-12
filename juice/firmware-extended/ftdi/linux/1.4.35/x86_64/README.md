# Intel/AMD 64-bit Linux

This target contains FTDI D2XX 1.4.35 for **64-bit Intel/AMD Linux** using
glibc, such as Ubuntu on a PC or laptop. This architecture is also called `amd64`.

For Raspberry Pi, use [aarch64](../aarch64/README.md) with a 64-bit OS,
or [armhf](../armhf/README.md) with a compatible 32-bit OS.

On Ubuntu, check the userspace architecture with:

```bash
dpkg --print-architecture
```

For this target, expect `amd64`. The application must also be built for
64-bit Intel/AMD.

Source package: `libftd2xx-linux-x86_64-1.4.35.tgz`.
The shared library was verified as ELF64 / x86-64.

`include/` contains the headers; `lib/` contains shared and static libraries.
See `README.pdf` for the original FTDI documentation and `SOURCE.txt` for
the archive checksum.

Build instructions: [BUILD-README.md](../../../../BUILD-README.md).
The Linux Makefile uses these local libraries and creates a distribution
directory for each architecture.

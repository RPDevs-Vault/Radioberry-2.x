# Intel/AMD 32-bit Linux

This target contains FTDI D2XX 1.4.35 for **32-bit x86 Linux applications**
using glibc. It is intended for a compatible 32-bit Intel/AMD environment,
or a 64-bit system with the required 32-bit compiler and runtime support.

For a standard 64-bit Ubuntu build, use [x86_64](../x86_64/README.md).
This x86 library is not intended for Raspberry Pi.

On Debian/Ubuntu, check the userspace architecture with:

```bash
dpkg --print-architecture
```

A native 32-bit x86 environment reports `i386`. A 64-bit system with 32-bit
support may report `amd64`; in that case, explicitly build the application
for 32-bit x86 and select matching libraries for all dependencies.

Source package: `libftd2xx-linux-x86_32-1.4.35.tgz`.
The shared library was verified as ELF32 / Intel 80386 (x86).

`include/` contains the headers; `lib/` contains shared and static libraries.
See `README.pdf` for the original FTDI documentation and `SOURCE.txt` for
the archive checksum.

Build instructions: [BUILD-README.md](../../../../BUILD-README.md).
The Linux Makefile uses these local libraries and creates a distribution
directory for each architecture.

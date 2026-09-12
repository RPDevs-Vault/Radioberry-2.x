# Linux FTDI D2XX 1.4.35

All required FTDI headers, libraries and documentation for the targets below
are already included. No separate FTDI download is needed to build or install
Radioberry Juice on these Linux targets.

**Raspberry Pi 64-bit uses the `aarch64` directory (ARM64).**

| Target | Intended environment | Target README |
| --- | --- | --- |
| `aarch64` | Raspberry Pi OS / Ubuntu, 64-bit ARM | [Raspberry Pi 64-bit](aarch64/README.md) |
| `armhf` | 32-bit ARMv7 hard-float Linux; not ARMv6-only Pi models | [Raspberry Pi 32-bit](armhf/README.md) |
| `x86_64` | 64-bit Intel/AMD Linux, such as Ubuntu on a PC | [Intel/AMD 64-bit](x86_64/README.md) |
| `x86_32` | 32-bit x86 Linux applications | [Intel/AMD 32-bit](x86_32/README.md) |

Directory layout for the Linux D2XX packages:

```text
1.4.35/
├── x86_64/       # 64-bit Intel/AMD Linux (Ubuntu)
│   ├── include/
│   └── lib/
├── x86_32/       # 32-bit Intel Linux
│   ├── include/
│   └── lib/
├── aarch64/      # 64-bit ARM Linux (Raspberry Pi OS / Ubuntu)
│   ├── include/
│   └── lib/
└── armhf/        # 32-bit ARMv7 hard-float Linux (not ARMv6)
    ├── include/
    └── lib/
```

These directories contain D2XX 1.4.35 imported from the downloaded FTDI packages.
Each architecture includes `SOURCE.txt` with the source archive name and its
SHA256 checksum for traceability. Checksums were calculated locally; they are
not independent verification against publisher-provided checksums.

Each architecture contains:

- Original `ftd2xx.h`, `WinTypes.h`, `Event.h` and `libusb.h` in `include/`,
  including their unchanged licence notices.
- `libftd2xx.so`, `libftd2xx.so.1.4.35` and `libftd2xx.a` in `lib/`.
- Original `README.pdf` and `release-notes.txt` beside `include/` and `lib/`.

The archives store `libftd2xx.so.1.4.35` as a symlink to `libftd2xx.so`.
Here the versioned alias is an identical regular file to support Windows
checkouts without symlink privileges. ELF inspection confirms that the shared
libraries use `libftd2xx.so` as their SONAME; include that file when deploying.

Choose the package for the installed OS architecture and ABI, not just the CPU.
The `armhf` package is ARMv7 hard-float and must not be used on ARMv6-only
Raspberry Pi models. Those require a separate ARMv6 package and directory.

Build instructions: [BUILD-README.md](../../../BUILD-README.md). Use `make -f linux-Makefile` from the firmware directory. The makefile selects these local libraries and packages the result in `dist/linux-<architecture>/`.

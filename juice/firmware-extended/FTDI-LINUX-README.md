# Radioberry Juice installation on Linux

**No separate FTDI download is required.** The repository already includes the
D2XX 1.4.35 headers, libraries and documentation for all included Linux targets.

Use the complete distribution produced by the build. It includes the same
architecture-specific FTDI D2XX **1.4.35** shared library used when linking
the application. A separate D2XX installation in `/usr/local/lib` is not needed.

For compilation instructions, see [BUILD-README.md](BUILD-README.md).
Version 1.4.35 applies to the Linux targets. Windows uses the separate
CDM 2.12.36.20 package; see [FTDI-WINDOWS-README](FTDI-WINDOWS-README).

## Installation script (recommended)

After building, run `install-linux.sh` on the target Linux machine, either
from `juice/firmware` or from the complete distribution directory. The script
detects the native userspace architecture and checks the executable and library.
It needs Bash, coreutils, dpkg and binutils (`readelf`). It does not download
dependencies, build the firmware, or start the radio.

Preview installation without making changes:

```bash
bash install-linux.sh --dry-run
```

Install the application and create any missing USB rules and group access:

```bash
sudo bash install-linux.sh
```

The script checks whether `/etc/udev/rules.d/99-radioberry.rules` and its helper
already exist. Existing files are preserved, including custom changes and
serial filters; missing files are created. It also creates the `radioberry`
group only if missing and adds the invoking user only if not already a member.
With `sudo`, the user is detected through `SUDO_USER`; `--user NAME` is only
needed when choosing a different account or running directly as root.

New rules match vendor/product ID `0403:6010`, including all connected FT2232
devices with that ID. A serial filter is optional if you want to limit new
rules to one device. Find the USB device serial (not an interface suffix):

```bash
sudo lsusb -v -d 0403:6010 | grep iSerial
```

Replace `YOUR_SERIAL` below with that value:

```bash
bash install-linux.sh --serial YOUR_SERIAL --dry-run
sudo bash install-linux.sh --serial YOUR_SERIAL
```

The installer:

- Copies the executable, gateware, matching D2XX library and FTDI notices to
  `/opt/radioberry-juice/`.
- Creates `/usr/local/bin/radioberry-juice`, a launcher that changes to the
  application directory before starting it.
- Creates `/home/pi/.radioberry/radioberry.props` from the template only if
  the configuration does not already exist.
- Ensures group access and creates missing rules/helper files by default.
  It reloads udev only when a rule or helper was created.
- The generated rule runs `/usr/local/libexec/radioberry-usb-unbind` when
  `ftdi_sio` binds to an interface matching `0403:6010` and the optional serial.
  The helper rechecks those matches and detaches only that interface. It never
  unloads kernel modules.

An existing rule is not overwritten even when `--serial` is provided or omitted
on a later run. Change existing filters manually if needed. Existence checking
does not prove that custom rules work: a permission-only rule remains
permission-only. Rules stored under other filenames are not merged automatically.
The known old Radioberry entry in `99-ftdisio.rules` is migrated automatically.
Serials may contain letters, digits, underscores and hyphens. If your USB
vendor/product ID differs, use a reviewed device-specific manual setup.

Stop the radio before reinstalling. Replaced application files get one
`.previous` backup; the active configuration, existing rule and existing helper
are preserved. If `99-ftdisio.rules` contains the old rule from this guide
(`0403:6010`, `MODE="0666"`, and `rmmod ftdi_sio && rmmod usbserial`), the
installer saves a unique `99-ftdisio.rules.backup.*` copy and removes only
that obsolete entry. Other entries are kept. The new USB setup is then used.
The dry-run explains this planned replacement and changes nothing; no manual
removal is needed. Unrecognized driver-removal rules get a message explaining
which file to edit and what to remove instead of being changed automatically.

After installation, log out and back in to refresh group membership, then
disconnect and reconnect the Radioberry. Review your configuration and start:

```bash
sudo nano /home/pi/.radioberry/radioberry.props
ldd /opt/radioberry-juice/radioberry-juice | grep ftd2xx
radioberry-juice
```

Expect `/opt/radioberry-juice/lib/libftd2xx.so` in the dependency output.
The installer reloads rules but does not detach a currently connected device;
reconnecting applies the new rules. No boot service is installed.

Use `--source /path/to/distribution` to select another build explicitly.
For packaging or testing, `--destdir /absolute/staging/directory` prefixes all
installation paths and skips host group changes and udev reload. A staged
installation is not a live installation; its launcher still uses `/opt/...`.

The following sections describe the distribution and the manual setup
alternative. Skip the manual permission-only rule if you used the installer;
replacing the generated rule would remove automatic interface release.

## 1. Select the matching Linux target

On Raspberry Pi OS or Ubuntu:

```bash
dpkg --print-architecture
```

| Userspace architecture | Build target | Distribution directory |
| --- | --- | --- |
| `arm64` (64-bit Raspberry Pi / ARM Linux) | `aarch64` | `dist/linux-aarch64/` |
| `armhf` (compatible 32-bit ARMv7 hard-float Linux) | `armhf` | `dist/linux-armhf/` |
| `amd64` (64-bit Intel/AMD Linux) | `x86_64` | `dist/linux-x86_64/` |
| `i386` (32-bit x86 Linux) | `x86_32` | `dist/linux-x86_32/` |

The bundled `armhf` library requires ARMv7; it does not support ARMv6-only
Pi models such as the original Pi 1 and Pi Zero/Zero W. Select for the OS
userspace, not just the CPU or the architecture reported by `uname -m`.

## 2. Build or copy the complete distribution

On the target machine, build from `juice/firmware`:

```bash
make -f linux-Makefile -j2
```

For Raspberry Pi 64-bit, the resulting directory is:

```text
dist/linux-aarch64/
├── install-linux.sh
├── radioberry-juice
├── gateware/CL016/radioberry.rbf
├── gateware/CL025/radioberry.rbf
├── radioberry.props
├── lib/
│   └── libftd2xx.so
└── ftdi/
    ├── README.pdf
    ├── release-notes.txt
    ├── SOURCE.txt
    └── ftd2xx.h
```

You may run this directory in place or copy the entire directory to the
Linux target. Preserve the executable permission on `radioberry-juice`.
Keep `lib/` beside the executable and retain the FTDI documentation and
licence notices. Do not deploy only the executable or mix files from
different architecture/version builds.

The executable contains a `$ORIGIN/lib` runtime search path. The build copies
the selected library directly from `ftdi/linux/1.4.35/<target>/lib/`.
The runtime filename is `libftd2xx.so`; it does not itself display the version.

No separate FTDI download, system-wide copy, symlink creation, or `ldconfig`
command is required for this layout. System libraries such as glibc must
still be compatible with the build; building directly on the target is the
simplest way to match them.

## 3. Verify the library used by the application

From the distribution directory (Raspberry Pi 64-bit example):

```bash
cd dist/linux-aarch64
ldd ./radioberry-juice | grep ftd2xx
```

Run this on the matching target architecture. Expect a path ending in:

```text
/linux-aarch64/lib/libftd2xx.so
```

It should resolve to the `lib/` directory beside this executable, not to
`/usr/local/lib`. An entry in `ldconfig -p` only describes the system cache;
its presence or absence does not verify this application's local library.

To compare the bundled file with the build input, run from `juice/firmware`:

```bash
sha256sum ftdi/linux/1.4.35/aarch64/lib/libftd2xx.so dist/linux-aarch64/lib/libftd2xx.so
```

The two hashes must match. Substitute your target for `aarch64` as needed.
`ftdi/SOURCE.txt` records the original archive name and its locally calculated
checksum; that archive checksum is different from the library file checksum.

An existing `LD_LIBRARY_PATH` or `LD_PRELOAD` setting can override normal
library selection. If `ldd` reports another copy, inspect those settings:

```bash
printenv LD_LIBRARY_PATH LD_PRELOAD
```

Correct the launch environment and recheck `ldd`. Keep existing system-wide
D2XX installations if other applications need them; no removal is required.

## 4. Set up USB access on the target machine

The bundled library does not configure Linux USB permissions or release
interfaces from a kernel driver. Perform this setup on the machine with
the Radioberry USB device attached.

Check the device:

```bash
lsusb
```

The FT2232 configuration used here has vendor/product ID `0403:6010`.
Confirm your device's actual ID before using the example rule. If `lsusb`
is unavailable, install the `usbutils` package.

Create a dedicated access group and add your login user:

```bash
sudo groupadd -f radioberry
sudo usermod -aG radioberry "$USER"
```

Run this from your normal login shell. Log out and back in after changing
group membership. If running as a service, its account needs group access too.

Create `/etc/udev/rules.d/99-radioberry.rules`:

```bash
sudo nano /etc/udev/rules.d/99-radioberry.rules
```

Add this single line:

```text
SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", ATTR{idVendor}=="0403", ATTR{idProduct}=="6010", GROUP="radioberry", MODE="0660"
```

This grants group access to matching USB devices. If other devices share this
ID, add an `ATTR{serial}=="YOUR_DEVICE_SERIAL"` match using the actual serial.
The rule sets permissions; it does not detach the serial driver.

Reload the rules, then disconnect and reconnect the device:

```bash
sudo udevadm control --reload-rules
```

## 5. Release the Radioberry interfaces from ftdi_sio

`ftdi_sio` is the kernel serial driver. `libftd2xx.so` is the userspace D2XX
library. D2XX needs access to the USB interfaces used by the application;
those interfaces must not remain claimed by `ftdi_sio`.

Inspect the bound interfaces:

```bash
ls -l /sys/bus/usb/drivers/ftdi_sio/
```

For a matching `/dev/ttyUSB0`, inspect its device ancestry:

```bash
udevadm info --attribute-walk --name=/dev/ttyUSB0
```

Confirm the USB device ID, serial, and interface identifier. An FT2232 has two
interfaces; examples are `1-1.1:1.0` and `1-1.1:1.1`. These identifiers depend
on USB topology. Replace the examples below with the actual Radioberry
interfaces, then release both:

```bash
echo -n '1-1.1:1.0' | sudo tee /sys/bus/usb/drivers/ftdi_sio/unbind
echo -n '1-1.1:1.1' | sudo tee /sys/bus/usb/drivers/ftdi_sio/unbind
```

Skip interfaces that are not bound. The module may remain loaded for other
serial devices; `lsmod` showing `ftdi_sio` does not mean it still owns the
Radioberry interfaces. This manual release must be repeated after reconnecting
or rebooting unless a device-specific automatic detach rule is configured.

Earlier versions of this guide used `99-ftdisio.rules` with global `rmmod`
commands and `MODE="0666"`. The installer automatically backs up and removes
that known old entry. For a fully manual setup, remove only the obsolete
Radioberry entry yourself and keep unrelated rules.
The new permission rule above deliberately does not automate driver detachment.

## 6. Configure and start the firmware

Follow the configuration section in [BUILD-README.md](BUILD-README.md).
The current Linux code still reads `/home/pi/.radioberry/radioberry.props`;
the copy beside the executable is a template, not the active Linux configuration.

Set `fpga=CL016` or `fpga=CL025` in the configuration. Start from the distribution directory so the program can find the selected file under `gateware/`:

```bash
./radioberry-juice
```

## Troubleshooting

| Symptom | Check |
| --- | --- |
| `libftd2xx.so` not found | Restore the matching `lib/` directory beside the executable; inspect `ldd`. |
| Another D2XX copy is selected | Inspect `LD_LIBRARY_PATH`, `LD_PRELOAD`, and the distribution contents. |
| Wrong ELF class / execution format error | Match the executable, library, and target userspace architecture. |
| `GLIBC_... not found` | Build on the target or use a toolchain/sysroot compatible with its OS. |
| `FT_Open failed` | Check device presence, USB permissions, interface binding, and whether another process owns the device. |
| Works only as root | Check group membership and permissions on the matching `/dev/bus/usb/...` device node. |
| Works until reconnect | Check that the existing rule includes automatic release; a preserved permission-only rule will not detach interfaces. Check any serial filter too. |

## Verification status and references

The x86_64 native build and ARM64 cross-build have been compiled and linked
in Ubuntu/WSL. The x86_64 dependency check resolved the bundled library, and
both binaries were checked for their architecture. These checks are not a
hardware test of the USB permission or interface-release procedure.
The installation script can be checked without altering the host using
`--dry-run` and `--destdir`; automatic detachment still needs a real-device test.
Staged installation tests passed for dry-run behavior, architecture and argument
validation, configuration preservation on reinstall, backup creation, rejection
of symlinked destinations, and generated udev rule syntax. Run them on x86_64
Ubuntu after building both distributions with `bash tests/install-linux-test.sh`.

- [Build instructions](BUILD-README.md)
- [FTDI Linux installation guide](https://ftdichip.com/wp-content/uploads/2020/08/AN_220_FTDI_Drivers_Installation_Guide_for_Linux-1.pdf)
- [Linux dynamic loader documentation](https://www.man7.org/linux/man-pages/man8/ld.so.8.html)
- [udev rule documentation](https://www.freedesktop.org/software/systemd/man/latest/udev.html)

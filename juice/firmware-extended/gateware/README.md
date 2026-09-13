# FPGA gateware

The CL016 and CL025 directories each contain the `radioberry.rbf` for
that FPGA variant.
Both directories are included in Linux and Windows distributions.

Set `fpga=CL016` or `fpga=CL025` in `radioberry.props` before starting.
The application loads `gateware/CL016/radioberry.rbf` or
`gateware/CL025/radioberry.rbf` relative to its working directory.
No file copying or renaming is needed. The displayed FPGA type reflects
this setting; it is not hardware detection.

On Windows, configuration is read from the working directory. On Linux,
`/home/pi/.radioberry/radioberry.props` is used if present, otherwise the
file in the working directory is used. Existing installations must add
the `fpga` setting to their existing configuration. A missing or invalid
setting stops startup before the device is opened.

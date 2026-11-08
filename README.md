# Kwrt BTF Build

Build a kernel-compatible detached BTF package for Kwrt.

## Target Device

- Device: Globitel BT-R320
- Firmware: Kwrt 25.12.2
- Target: mediatek/filogic
- Architecture: aarch64_cortex-a53
- Kernel: 6.12.74
- Kernel package: 6.12.74~1-r1
- Package format: IPK
- Package manager: opkg

## Objective

Generate a detached kernel BTF file using the exact
kernel source, configuration and toolchain corresponding
to the target Kwrt firmware.

Package the resulting BTF as an IPK.

## Compatibility Requirements

The build must verify:

- Kernel version
- Kernel vermagic
- Kernel release
- Target architecture
- BTF structure validity

A package must not be published unless all compatibility
checks pass.

## Important

This project does not replace or upgrade the router firmware
or kernel.

The generated BTF package is intended only for the exact
target kernel configuration.

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

## Workflows

| Workflow | Purpose |
|----------|---------|
| `kwrt-diagnose.yml` | Resolve Kwrt commit/config snapshot for a build date |
| `kwrt-source-lock.yml` | Pin OpenWrt / ImmortalWrt / LEDE / Kwrt commits |
| `kwrt-replay-config.yml` | Reproduce historical `.config` without building |
| `kwrt-build-btf.yml` | Build matching detached BTF **IPK** with hard gates |

### `kwrt-build-btf.yml` gates (must all pass)

1. Resolved `LINUX_VERSION` == `6.12.74` (from pinned OpenWrt tree)
2. `CONFIG_TARGET_ARCH_PACKAGES` == `aarch64_cortex-a53`
3. Board/subtarget == `mediatek` / `filogic`
4. BTF-related kconfig active (`DEBUG_INFO`, not `REDUCED`, `DEBUG_INFO_BTF`)
5. Output package is **IPK** (opkg), not apk
6. Control metadata `Version` contains kernel version; `Architecture` matches
7. Payload contains `/usr/lib/debug/boot/vmlinux` and `bpftool btf dump` succeeds

Only after these checks pass is the artifact uploaded.

### Source pins (build date 2026-03-30)

- Kwrt: `ab0b3066ed2152a1785fd7d4dba8a10b08b2e5c0`
- OpenWrt: `d266501ad6188cce279a487b1ce61f4f327cd496` → kernel **6.12.74**
- ImmortalWrt: `8a18e84a887379db8954449a9fabe6df54084dea`
- LEDE: `7b303a8ddacdd798c1cdef0ef4f015fa50597390`

### Install on device (after gates pass)

```sh
# Confirm device has no in-kernel BTF and matching version
uname -r                    # expect 6.12.74
[ -e /sys/kernel/btf/vmlinux ] && echo "already has BTF" || echo "need detached BTF"

opkg install ./vmlinux-btf_*.ipk
ls -l /usr/lib/debug/boot/
```

# embedded-linux-edge-platform

Yocto based embedded Linux platform for edge devices with support for
networking, Qt applications, CAN communication, and robust OTA updates.

## Overview

This repository contains the custom Yocto layer (`meta-edge-platform`) used to build the
platform image and system configuration for Raspberry Pi-based bring-up.

Key features of the platform include:

- **Robust Boot & Recovery:** Enabled U-Boot with custom A/B partition boot routing, boot-counting, and automatic fallback logic embedded in `boot.cmd.in`.
- **A/B Partitioning:** Custom wic layout (`edge-platform-dual.wks.in`) creating 3 partitions: boot (FAT32), Rootfs A (ext4), and Rootfs B (ext4).
- **Modern Service Management:** Built around `systemd` as the primary init manager instead of sysvinit.
- **Fail-Safe OTA Updates:** Includes the `boot-mark-good` systemd timer+service combo that evaluates OS stability over the first 30 seconds of uptime before finalizing an update slot.
- **Connectivity:** Wi-Fi provisioning through `wpa_supplicant`, and OpenSSH enabled directly on boot.

The project is intended to grow with additional Qt applications, CAN services,
and broader platform integration over time.

## Build Notes

1. Ensure your layer is added to `bblayers.conf`.
2. Configure your build to use our custom distribution and enable the hardware configurations by adding this to your `build-rpi/conf/local.conf`:

```bitbake
DISTRO ?= "edge-platform"
MACHINE ?= "raspberrypi4-64"
RPI_USE_U_BOOT = "1"
INHERIT += "rm_work"
```

3. Build the platform image:

```bash
bitbake edge-platform-image
```

## Secrets

This repository is kept free of real credentials.

Local secrets such as Wi-Fi credentials and the root password hash should be
provided through your build configuration, for example in:

```text
build-rpi/conf/local.conf
```

## License

This layer is licensed under the MIT license. See [COPYING.MIT](COPYING.MIT).

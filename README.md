# embedded-linux-edge-platform

Yocto based embedded Linux platform for edge devices with support for
networking, Qt applications, CAN communication, and robust OTA updates.

## Overview

This repository contains the custom Yocto layer (`meta-edge-platform`) used to build the
platform image and system configuration for Raspberry Pi-based bring-up.

Key features of the platform include:

- **Robust Boot & Recovery:** Enabled U-Boot with custom A/B partition boot routing, boot-counting, and automatic fallback logic embedded in `boot.cmd.in`.
- **Read-Only Core:** The primary OS is fully compiled as a Read-Only RootFS to guarantee zero file-corruption during aggressive power losses.
- **Native OverlayFS:** Leverages Yocto's `overlayfs` and `overlayfs-etc` classes to transparently mount `/etc`, `/var`, and `/home` over the `/data` partition, providing a completely persistent, read-write feel to the locked-down operating system.
- **4-Disk Partitioning:** Custom wic layout (`edge-platform-dual.wks.in`) creating 4 partitions: Boot (FAT32), Rootfs A (ext4), Rootfs B (ext4), and a dedicated Data (ext4) partition for overlays and app data.
- **Modern Service Management:** Built around `systemd` as the primary init manager instead of sysvinit.
- **Fail-Safe OTA Updates:** Includes the `boot-mark-good` systemd timer+service combo that evaluates OS stability over the first 30 seconds of uptime before finalizing an update slot.
- **Connectivity & Security:** Wi-Fi provisioning through `wpa_supplicant`, and OpenSSH enabled directly on boot with all passwords explicitly disabled in favor of Asymmetric Keys.

The project is intended to grow with additional Qt applications, CAN services,
and broader platform integration over time.

## Tools & Technologies Used

- **Yocto Project (Poky):** The core build framework used to generate the tiny, purpose-built embedded Linux distribution from source.
- **WIC (OpenEmbedded Image Creator):** Tool used to generate the final partitioned OS image (`.wic.bz2`) encompassing the boot sector, root filesystems, and partition table based on our `.wks` design.
- **U-Boot:** The open-source primary bootloader. Chosen for its robust scripting abilities, allowing us to route booting between A/B software slots and automatically rollback bad flashes.
- **libubootenv:** Provides the user-space `fw_printenv` and `fw_setenv` utilities, allowing the running Linux OS to read and modify the U-Boot hardware environment safely.
- **systemd:** The Linux init system and service manager. It allows us to predictably sequence hardware bring-up and provides precise timer components (used for our 30-sec `boot-mark-good` validation trigger).
- **wpa_supplicant & iw:** User-space tools integrated to establish headless WPA/WPA2 wireless networking out of the box.
- **OpenSSH (Secured):** Standard suite for secure headless administration via ssh. The build explicitly configures `sshd_config` to reject all password authentication, accepting only public SSH keys baked into the ROM.
- **ext4:** The reliable standard Linux filesystem chosen to format our dual root partitions.
- **rpidistro-bcm43456:** The proprietary Broadcom firmware blobs explicitly deployed to drive the Raspberry Pi 4's Wi-Fi and Bluetooth radios.

## Build Notes

1. Ensure your layer is added to `bblayers.conf`.
2. Configure your build to use our custom distribution and enable the hardware configurations by adding this to your `build-rpi/conf/local.conf`:

```bitbake
DISTRO ?= "edge-platform"
MACHINE ?= "raspberrypi4-64"
RPI_USE_U_BOOT = "1"
INHERIT += "rm_work"

# Provide your Master SSH Key here (It will be embedded into the Read-Only OS)
ROOT_SSH_AUTHORIZED_KEYS = "ssh-ed25519 AAAAC3Nz... dev@workstation"
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

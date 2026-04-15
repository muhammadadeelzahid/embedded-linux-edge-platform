# embedded-linux-edge-platform

Yocto based embedded Linux platform for edge devices with support for
networking, Qt applications, and CAN communication.

## Overview

This repository currently contains the custom Yocto layer used to build the
platform image and system configuration for Raspberry Pi-based bring-up.
The layer currently includes:

- the `edge-platform-image` image recipe
- Wi-Fi provisioning through `wpa_supplicant`
- SSH enabled on boot
- UART console support
- Raspberry Pi firmware and module selection needed for networking

The project is intended to grow with additional Qt applications, CAN services,
and broader platform integration over time.

## Layer Name

The Yocto layer directory is:

```text
meta-edge-platform
```

## Build Notes

Add the layer to `bblayers.conf`, then build:

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

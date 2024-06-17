# Rugpi: Quick Start Template for Mender

This template showcases how to build a Rugpi image with [Mender](https://mender.io) integration.

For general information about Rugpi and how to use it, check out [Rugpi's documentation](https://oss.silitics.com/rugpi/docs/getting-started).

You can build images for:

- Raspberry Pi 4 and 5 (ARM64).
- Any EFI-compatible system (ARM64 and AMD64).

For Raspberry Pi, images can be built based on Raspberry Pi OS or Debian.
Note that Rugpi also supports older models of Raspberry Pi, however, we do not showcase images for them here.

The images are ready-to-use and devices running them connect to the Mender's device management platform automatically.
To this end, you must configure your tenant/organization token (details bellow).
Note that while fully compatible with Mender's cloud offering and update client, **the actual update is handled by Rugpi**.
In this setup, Mender only serves as a frontend for Rugpi's OTA update mechanism.

## 🔧 Configuration

To configure Mender, you need your Mender tenant/organization token.
Note that this token, as any secret, should not be committed to Git.
For this reason, we use a `.env` file for secrets.
To configure the token, copy the `env.template` file to `.env` and replace the placeholder with your actual token.
In addition, you may need to change the server URL in the [`mender.conf`](recipes/mender/files/mender.conf) configuration file.
If you want to be able to connect via SSH, put your public SSH key in the respective layer configuration file in [`layers`](layers).

## 🏗️ Building Images

To build an image for Raspberry Pi 4, including the necessary firmware update:

```bash
./run-bakery bake image rpi-raspios-pi4
```

To build an image for Raspberry Pi 5 or 4, without the firmware update:

```bash
./run-bakery bake image rpi-raspios
```

To create a Mender artifact from the produced `rpi-raspios` image:

```bash
VERSION=$(date +'%Y%m%d.%H%M')
mender-artifact write module-image \
    -n "Image ${VERSION}" \
    -t raspberrypi4 \
    -T rugpi-image \
    -f build/images/rpi-raspios.img \
    -o build/${VERSION}.mender \
    --software-name "Rugpi Image" \
    --software-version "${VERSION}"
```

## ℹ️ Remarks

### GitHub Actions

This repository contains a workflow for GitHub Actions which builds both images (with and without the firmware update for Raspberry Pi 4) and a Mender artifact.
To inject the Mender tenant token, you need to create a GitHub Actions secret named `ENV` and put the contents of the `.env` file there.
Note that the build artifacts contain the token and are thus not uploaded by default.
If you want to extract the artifacts, uncomment the respective section in the workflow.
**Make sure your repository is private in order to not leak the token.**

### Simple SBOM

As part of the image building process, a simple *software bill of materials* (SBOM) is generated.
The SBOM is stored in `build/customized.sbom.txt` and also included in the build artifacts of the GitHub Actions workflow.

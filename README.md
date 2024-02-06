# Rugpi: Quick Start Template for Mender

This template shows how to build a Rugpi image with Mender integration.

For general information about Rugpi and how to use it, check out the [Rugpi's documentation](https://oss.silitics.com/rugpi/docs/getting-started).

To configure Mender, you need to adapt the configuration files found in the [`mender`](recipes/mender) recipe.
For a minimal working configuration, you should put your Mender tenant token in the [`mender.conf`](recipes/mender/files/mender.conf) configuration file and your public SSH key in [`layers/customized.toml`](layers/customized.toml).

To build an image for Raspberry Pi 4, including the necessary firmware update:

```bash
./run-bakery bake image pi4 build/pi4.img
```

To build an image for Raspberry Pi 5 or 4, without the firmware update:

```bash
./run-bakery bake image tryboot build/tryboot.img
```

To create a Mender artifact from the produced images:

```bash
VERSION=$(date +'%Y%m%d.%H%M')
mender-artifact write module-image \
    -n "Image ${VERSION}" \
    -t raspberrypi4 \
    -T rugpi-image \
    -f build/tryboot.img \
    -o build/${VERSION}.mender \
    --software-name "Mender Template" \
    --software-version "${VERSION}"
```
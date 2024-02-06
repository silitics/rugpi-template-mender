# Rugpi: Quick Start Template for Mender

This template shows how to build a Rugpi image with Mender integration.

For general information about Rugpi, check out the [Rugpi quick start guide](https://oss.silitics.com/rugpi/docs/getting-started).

To configure Mender, you need to adapt the configuration files found in the [`mender`](recipes/mender) recipe.
For a minimal working configuration, you should put your Mender tenant token in the [`mender.conf`](recipes/mender/files/mender.conf) file and your public SSH key in `layers/customized.toml`.

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
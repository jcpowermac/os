# os &nbsp; [![bluebuild build badge](https://github.com/jcpowermac/os/actions/workflows/build.yml/badge.svg)](https://github.com/jcpowermac/os/actions/workflows/build.yml)

See the [BlueBuild docs](https://blue-build.org/how-to/setup/) for quick setup instructions for setting up your own repository based on this template.

After setup, it is recommended you update this README to describe your custom image.

## Build prerequisites

Building a new image of this repository requires:

- [BlueBuild CLI](https://github.com/blue-build/cli?tab=readme-ov-file#installation), installed via cargo, the installer container, the install script, distrobox, or nix.
- A container builder: `podman` (≥ v4), `docker` (≥ v23), or `buildah` (≥ v1.29).
- `git` and, for signing, [`cosign`](https://github.com/sigstore/cosign) or `skopeo`.

GitHub builds (`.github/workflows/build.yml`) need nothing extra — the [blue-build/github-action](https://blue-build.org/reference/github-action/) provides the toolchain; the only requirement is the `SIGNING_SECRET` repository secret.

To build locally:

```bash
bluebuild build ./recipes/recipe.yml
```

## Installation

> [!WARNING]  
> [This is an experimental feature](https://www.fedoraproject.org/wiki/Changes/OstreeNativeContainerStable), try at your own discretion.

To rebase an existing atomic Fedora installation to the latest build:

- First rebase to the unsigned image, to get the proper signing keys and policies installed:
  ```
  rpm-ostree rebase ostree-unverified-registry:ghcr.io/jcpowermac/os:latest
  ```
- Reboot to complete the rebase:
  ```
  systemctl reboot
  ```
- Then rebase to the signed image, like so:
  ```
  rpm-ostree rebase ostree-image-signed:docker://ghcr.io/jcpowermac/os:latest
  ```
- Reboot again to complete the installation
  ```
  systemctl reboot
  ```

The `latest` tag will automatically point to the latest build. That build will still always use the Fedora version specified in `recipe.yml`, so you won't get accidentally updated to the next major version.

## ISO

If build on Fedora Atomic, you can generate an offline ISO with the instructions available [here](https://blue-build.org/how-to/generate-iso/). These ISOs cannot unfortunately be distributed on GitHub for free due to large sizes, so for public projects something else has to be used for hosting.

## Verification

These images are signed with [Sigstore](https://www.sigstore.dev/)'s [cosign](https://github.com/sigstore/cosign). You can verify the signature by downloading the `cosign.pub` file from this repo and running the following command:

```bash
cosign verify --key cosign.pub ghcr.io/jcpowermac/os
```

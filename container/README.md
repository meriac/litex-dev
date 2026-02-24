# LiteX Development Containers

Container images for LiteX FPGA development, published to `ghcr.io/meriac/litex-dev`.

## Images

| Image | Description |
|-------|-------------|
| `litex-dev:<arch>` | Base LiteX development environment with Python, LiteX and standard config |
| `litex-dev-riscv:<arch>` | Extends litex-dev with RISC-V cross-compiler (`gcc-riscv64-unknown-elf`), meson and ninja |

Supported architectures: `amd64`, `arm64`

## Prerequisites

- [Podman](https://podman.io/) installed
- For publishing: a GitHub personal access token (see [Publishing](#publishing))

## Quick Start

Pull and run the pre-built image directly:

```sh
podman run --rm -it ghcr.io/meriac/litex-dev:latest
```

Or with a persistent workspace mounted from your host:

```sh
mkdir -p workspaces

podman run \
  -h litex-dev \
  --rm \
  -v $PWD/workspaces:/workspaces \
  -it ghcr.io/meriac/litex-dev:latest
```

For the RISC-V variant (includes cross-compiler, meson and ninja):

```sh
podman run \
  -h litex-dev \
  --rm \
  -v $PWD/workspaces:/workspaces \
  -it ghcr.io/meriac/litex-dev-riscv:latest
```

## Building and Running Locally

> **Note:** `make run` launches a locally built image. You must run `make build` first, or use `make rebuild` to build and run in one step.

Build both container images for your host architecture:

```sh
make build
```

Launch an interactive shell in the locally built base container:

```sh
make run
```

Or clean, build and run in one step:

```sh
make rebuild
```

The `workspaces/` directory is bind-mounted into the container at `/workspaces`.

The build produces two images:
1. `ghcr.io/meriac/litex-dev:<arch>` from `Containerfile`
2. `ghcr.io/meriac/litex-dev-riscv:<arch>` from `Containerfile.RISCV`

To rebuild from scratch and launch:

```sh
make rebuild
```

## Publishing

You need a GitHub personal access token (classic) with the following scopes:
- **`write:packages`** — push container images to GitHub Container Registry
- **`read:packages`** — pull private container images
- **`delete:packages`** — (optional) remove old image versions

Generate one at [github.com/settings/tokens](https://github.com/settings/tokens) and add it to `~/.bashrc` to avoid passing it on every login:

```sh
export CR_PAT=<your-token>
```

Then login to the GitHub Container Registry:

```sh
make login
```

Push the images:

```sh
make publish
```

## Multi-arch Manifest

After building and publishing images on both `amd64` and `arm64` hosts, create and push a multi-arch manifest:

```sh
make manifest
```

This creates a `latest` tag that resolves to the correct architecture automatically.

## Other Targets

| Target | Description |
|--------|-------------|
| `make status` | Show image layer tree |
| `make version` | Print the full image name |
| `make clean` | Remove built images |
| `make clean_all` | Remove all containers and prune podman storage |

## License

Apache 2.0 - see [LICENSE](../LICENSE)

## Kernel images

The builder provides firecracker-compatible images for multiple kernel versions
and **architectures** (amd64 and arm64), and cloudhypervisor-compatible images for
amd64 (`5.12`, `5.15.12`), using the [cloud-hypervisor/linux](https://github.com/cloud-hypervisor/linux) branches.

Builds are parameterized by `ARCH` (default `amd64`). Use `make build-fc ARCH=arm64` for Firecracker arm64 images.

These images are extremely minimal. `kernel-bin` is built from `SCRATCH` and
only contains a kernel binary. `kernel-modules`, also built from `SCRATCH`,
only contains modules for that kernel.

Image files can be found in `kernel/`. The full chain of images which go into
creating the eventually used `bin` and `module` images is:

- `kernel/builder` - Built from Ubuntu `20.04`, installs packages required to build kernels. Tagged per arch (e.g. `kernel-builder-amd64:dev`, `kernel-builder-arm64:dev`).
- `kernel/firecracker/base` - Built from the `builder` image, compiles the kernel and modules
	from `kernel/firecracker/configs/<arch>/` (e.g. `configs/amd64/`, `configs/arm64/`). This image can be used in specs for versions older than
	[Flintlock 0.5.0][fl5] and [CAPMVM][cap8] which do not support the separate bin and module
	images.
- `kernel/cloud-hypervisor/base` - Built from the `builder` image, compiles the kernel and modules
	from `kernel/cloud-hypervisor/configs/amd64/`.
- `kernel/bin` - Built from either the firecracker or cloud-hypervisor base, contains only the kernel binary.
- `kernel/modules` - Built from either the firecracker or cloud-hypervisor base, contains only the kernel modules.

## Using images

The resulting images can be added to Microvm specs like so (amd64 and arm64):

```
ghcr.io/liquidmetal-dev/firecracker-kernel-bin-amd64:X.X.X
ghcr.io/liquidmetal-dev/firecracker-kernel-modules-amd64:X.X.X

ghcr.io/liquidmetal-dev/firecracker-kernel-bin-arm64:X.X.X
ghcr.io/liquidmetal-dev/firecracker-kernel-modules-arm64:X.X.X

ghcr.io/liquidmetal-dev/cloudhypervisor-kernel-bin-amd64:X.X.X
ghcr.io/liquidmetal-dev/cloudhypervisor-kernel-modules-amd64:X.X.X
```

For platforms using versions older than [Flintlock `0.5.0`][fl5] and [CAPMVM `0.8.0`][cap8]
use the following legacy image for **firecracker** microvms (amd64):

```
ghcr.io/liquidmetal-dev/flintlock-kernel:X.X.X
```

**Note** the `filename` locations for each of the kernels:
- If using the firecracker `kernel` or `kernel-bin`, the value of `filename`
	should be `bin/vmlinux`
- If using the cloud-hypervisor `kernel-bin`, the value of `filename`
	should be `bin/vmlinux.bin`

## Publishing new images

New images are published automatically when merged to main by Github Actions.
You should not need to manually publish new images to the `ghcr` account.

### Adding new Kernel versions

- Add a new kernel config file in `kernel/<FC|CH>/configs/<arch>/` (e.g. `firecracker/configs/amd64/linux-x86_64-X.Y.Z.config`, `firecracker/configs/arm64/linux-arm64-X.Y.Z.config`).
- Update the list of `FC_KERNEL_VERSIONS` or `CH_KERNEL_VERSIONS` in `kernel/Makefile`
- Update the versions lists (and matrix if needed) in `.github/workflows/kernel-images.yml` and `.github/workflows/kernel-images-manual.yml`
- Update any docs (like this one) with new version info
- Update the [Liquid Metal docs][lm-docs] with new version info
- Open a PR with your changes
- Merge once checks and reviews pass

Your new version will be automatically published.

You can also trigger a build and release manually from the Actions tab: **Build and release kernel images** → "Run workflow".

### Kubernetes compatibility (e.g. 1.34.x)

For running Kubernetes 1.34.x (and similar) inside microVMs, use **5.10.77** or **5.2.21** (Firecracker) or **5.12** or **5.15.12** (Cloud Hypervisor). These meet Kubernetes kernel requirements (PSI 4.20+, cgroup v2 5.8+, runc freezer 5.2+). Kernel **4.19.215** is below the recommended minimums and is not recommended for current Kubernetes versions.

## Local development and custom images

_Note that Firecracker only supports `5.10` and `4.19` kernels._

1. Fork this repo, clone your fork
1. `cd` into `kernel`
1. Edit the `Dockerfile` for the image you want to change.
	The build sequence is as follows:
	```mermaid
		graph TD;
			builder-->fc-base;
			builder-->ch-base;
			fc-base-->fc-bin;
			ch-base-->ch-bin;
			fc-base-->fc-modules;
			ch-base-->ch-modules;
	```
1. Set your image registry `export REGISTRY=docker.io/foobar`
1. Optionally set `ARCH=arm64` for arm64 images (default is amd64). For arm64 you need Docker Buildx.
1. If you only want to build one or a subset of kernel versions, set this with
	`export FC_KERNEL_VERSIONS=5.10.77` or `export CH_KERNEL_VERSIONS=5.12`
1. Run `make build-fc` or `make build-ch` to build the images and `make push-fc`
	or `make push-ch` to push the images to your registry

### Build pipeline notes

- **Firecracker base** clones the kernel from kernel.org over HTTPS (CI-friendly).
- If a kernel build fails, check that a config file exists in `firecracker/configs/<arch>/` or `cloud-hypervisor/configs/amd64/` for the exact version (e.g. `linux-x86_64-5.10.77.config`, `linux-arm64-6.1.102.config`).

[fl5]: https://github.com/liquidmetal-dev/flintlock/releases/tag/v0.5.0
[cap8]: https://github.com/liquidmetal-dev/cluster-api-provider-microvm/releases/tag/v0.8.0
[lm-docs]: https://github.com/liquidmetal-dev/site/blob/main/docs/guides/images.md

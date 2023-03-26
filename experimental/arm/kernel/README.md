## Kernel images

**This is an experimental builder for ARM images.**

**I have only done Firecracker for now (to get a demo working), in the future we
should also have for Cloud Hypervisor.**

**Also in the future, this dir should be merged in with the other so that we
always create multi-arch images with minimal duplication. So far there are only
2 lines in the base image which differ, so this should be easy to do.**

The builder provides firecracker-compatible images for 1 kernel version, `5.10.x`.

These images are extremely minimal. `kernel-bin-arm` is built from `SCRATCH` and
only contains a kernel binary. `kernel-modules-arm`, also built from `SCRATCH`,
only contains modules for that kernel.

Image files can be found in `kernel/`. The full chain of images which go into
creating the eventually used `bin` and `module` images is:

- `kernel/builder` - Built from Ubuntu `20.04`, installs packages required to build kernels.
- `kernel/firecracker/base` - Built from the `builder` image, compiles the kernel and modules
	from `kernel/firecracker/configs`. This image can be used in specs for versions older than
	[Flintlock 0.5.0][fl5] and [CAPMVM][cap8] which do not support the separate bin and module
	images.
- `kernel/bin` - Built from the firecracker base, contains only the kernel binary.
- `kernel/modules` - Built from the firecracker base, contains only the kernel modules.

## Using images

The resulting images can be added to Microvm specs like so:

```
ghcr.io/weaveworks-liquidmetal/firecracker-kernel-bin-arm:X.X.X
ghcr.io/weaveworks-liquidmetal/firecracker-kernel-modules-arm:X.X.X
```

For platforms using versions older than [Flintlock `0.5.0`][fl5] and [CAPMVM `0.8.0`][cap8]
use the following legacy image for **firecracker** microvms:

```
ghcr.io/weaveworks-liquidmetal/flintlock-kernel-arm:X.X.X
```

**Note** the `filename` locations for each of the kernels:
- If using the firecracker `kernel` or `kernel-bin`, the value of `filename`
	should be `bin/image`

## Publishing new images

New images should eventually published automatically when merged to main by Github Actions.
I have not added these for now.

### Adding new Kernel versions

- Add a new kernel config file in `kernel/firecracker/configs` following the naming pattern
- Update the list of `FC_KERNEL_VERSIONS` in `kernel/Makefile`
- Update any docs (like this one) with new version info
- Update the [Liquid Metal docs][lm-docs] with new version info
- Open a PR with your changes
- Merge once checks and reviews pass

Your new version will be automatically published.

## Local development and custom images

_Note that Firecracker only supports `5.10` and `4.19` kernels._

1. Fork this repo, clone your fork
1. `cd` into `experimental/arm/kernel`
1. Edit the `Dockerfile` for the image you want to change.
	The build sequence is as follows:
	```mermaid
		graph TD;
			builder-->fc-base;
			fc-base-->fc-bin;
			fc-base-->fc-modules;
	```
1. Eventually we would want something like
	```mermaid
		graph TD;
			multi-arch builder-->fc-base-x86;
			multi-arch builder-->ch-base-x86;
			multi-arch builder-->fc-base-arm64;
			multi-arch builder-->ch-base-arm64;
			fc-base-x86>multi-arch fc-bin;
			ch-base-x86>multi-arch ch-bin;
			fc-base-arm64-->multi-arch fc-bin;
			ch-base-arm64-->multi-arch ch-bin;
			fc-base-x86-->multi-arch fc-modules;
			ch-base-x86-->multi-arch ch-modules;
			fc-base-arm64-->multi-arch fc-modules;
			ch-base-arm64-->multi-arch ch-modules;
	```
1. Set your image registry `export REGISTRY=docker.io/foobar`
1. If you only want to build one or a subset of kernel versions, set this with
	`export FC_KERNEL_VERSIONS=5.10.77`
1. Run `make build-and-push-fc` to build and push the images to your registry

[fl5]: https://github.com/weaveworks-liquidmetal/flintlock/releases/tag/v0.5.0
[cap8]: https://github.com/weaveworks-liquidmetal/cluster-api-provider-microvm/releases/tag/v0.8.0
[lm-docs]: https://github.com/weaveworks-liquidmetal/site/blob/main/docs/guides/images.md

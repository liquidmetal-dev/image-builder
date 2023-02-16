## Kernel images

The builder provides firecracker-compatible images for 2 kernel versions,
`5.10.x` and `4.19.x`, and cloudhypervisor-compatible images for 1 kernel
version, `5.12`.

These images are extremely minimal. `kernel-bin` is built from `SCRATCH` and
only contains a kernel binary. `kernel-modules`, also built from `SCRATCH`,
only contains modules for that kernel.

Image files can be found in `kernel/`. The full chain of images which go into
creating the eventually used `bin` and `module` images is:

- `kernel/builder` - Built from Ubuntu `20.04`, installs packages required to build kernels.
- `kernel/firecracker/base` - Built from the `builder` image, compiles the kernel and modules
	from `kernel/firecracker/configs`. This image can be used in specs for versions older than
	[Flintlock 0.5.0][fl5] and [CAPMVM][cap8] which do not support the separate bin and module
	images.
- `kernel/cloud-hypervisor/base` - Built from the `builder` image, compiles the kernel and modules
	from `kernel/cloud-hypervisor/configs`.
- `kernel/bin` - Built from either the firecracker or cloud-hypervisor base, contains only the kernel binary.
- `kernel/modules` - Built from either the firecracker or cloud-hypervisor base, contains only the kernel modules.

## Using images

The resulting images can be added to Microvm specs like so:

```
ghcr.io/weaveworks-liquidmetal/firecracker-kernel-bin:X.X.X
ghcr.io/weaveworks-liquidmetal/firecracker-kernel-modules:X.X.X

ghcr.io/weaveworks-liquidmetal/cloudhypervisor-kernel-bin:X.X.X
ghcr.io/weaveworks-liquidmetal/cloudhypervisor-kernel-modules:X.X.X
```

For platforms using versions older than [Flintlock `0.5.0`][fl5] and [CAPMVM `0.8.0`][cap8]
use the following legacy image for **firecracker** microvms:

```
ghcr.io/weaveworks-liquidmetal/flintlock-kernel:X.X.X
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

- Add a new kernel config file in `kernel/<FC/CH>/configs` following the naming pattern
- Update the list of `FC_KERNEL_VERSIONS` or `CH_KERNEL_VERSIONS` in `kernel/Makefile`
- Update the versions lists in `.github/workflows/kernel-images.yml` and `.github/workflows/kernel-images-manual.yml`
- Update any docs (like this one) with new version info
- Update the [Liquid Metal docs][lm-docs] with new version info
- Open a PR with your changes
- Merge once checks and reviews pass

Your new version will be automatically published.

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
1. If you only want to build one or a subset of kernel versions, set this with
	`export FC_KERNEL_VERSIONS=5.10.77` or `export CH_KERNEL_VERSIONS=5.12`
1. Run `make build-fc` or `make build-ch` to build the images and `make push-fc`
	or `make push-ch` to push the images to your registry

[fl5]: https://github.com/weaveworks-liquidmetal/flintlock/releases/tag/v0.5.0
[cap8]: https://github.com/weaveworks-liquidmetal/cluster-api-provider-microvm/releases/tag/v0.8.0
[lm-docs]: https://github.com/weaveworks-liquidmetal/site/blob/main/docs/guides/images.md

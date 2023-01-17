## Kernel images

The builder provides firecracker-compatible images for 2 kernel versions,
`5.10.x` and `4.19.x`.
These images are extremely minimal. `kernel-bin` is built from `SCRATCH` and
only contains a kernel binary. `kernel-modules`, also built from `SCRATCH`,
only contains modules for that kernel.

Image files can be found in `kernel/`. The full chain of images which go into
creating the eventually used `bin` and `module` images is:

- `kernel/builder` - Built from Ubuntu `20.04`, installs packages required to build kernels.
- `kernel/base` - Built from the `builder` image, compiles the kernel and modules
	from `kernel/configs`. This image can be used in specs for versions older than
	[Flintlock 0.5.0][fl5] and [CAPMVM][cap8] which do not support the separate bin and module
	images.
- `kernel/bin` - Built from the base, contains only the kernel binary.
- `kernel/modules` - Built from the base, contains only the kernel modules.

The resulting images can be added to Microvm specs like so:

```
ghcr.io/weaveworks-liquidmetal/kernel-bin:X.X.X
ghcr.io/weaveworks-liquidmetal/kernel-modules:X.X.X
```

For platforms using versions older than [Flintlock `0.5.0`][fl5] and [CAPMVM `0.8.0`][cap8]
us the following legacy image:

```
ghcr.io/weaveworks-liquidmetal/flintlock-kernel:X.X.X

or

ghcr.io/weaveworks-liquidmetal/flintlock-kernel:X.X.X
```

## Publishing new images

New images are published automatically when merged to main by Github Actions.
You should not need to manually publish new images to the `ghcr` account.

### Adding new Kernel versions

- Add a new kernel config file in `kernel/configs` following the naming pattern
- Update the list of `KERNEL_VERSIONS` in `kernel/Makefile`
- Update the versions list in `.github/workflows/kernel-images.yml` and `.github/workflows/kernel-images-manual.yml`
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
			builder-->base;
			base-->bin;
			base-->modules;
	```
1. Set your image registry `export REGISTRY=docker.io/foobar`
1. If you only want to build one or a subset of kernel versions, set this with
	`export KERNEL_VERSIONS=5.10.77`
1. Run `make build` to build the images and `make push` to push the images to your
	registry

[fl5]: https://github.com/weaveworks-liquidmetal/flintlock/releases/tag/v0.5.0
[cap8]: https://github.com/weaveworks-liquidmetal/cluster-api-provider-microvm/releases/tag/v0.8.0
[lm-docs]: https://github.com/weaveworks-liquidmetal/site/blob/main/docs/guides/images.md

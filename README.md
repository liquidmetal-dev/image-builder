# image-builder
Image building for Weaveworks projects.


## How to build your own custom images

1. Fork this repo, clone your fork
1. There are several images here, each building from the last, key ones are:
	- `flintlock/base-ubuntu` - the base of the rootfs for any microvm
	- `flintlock/kernel/builder` - the base/builder image for the kernel
	- `flintlock/kernel` - the kernel for any microvm
	- `capmvm/kubernetes` - the rootfs for any microvm
1. Make changes accordingly
1. Build your images from the point of the change, the sequence is as follows,
	jump in where you need to.

	```bash
	export REGISTRY=docker.io/your-reg

	cd flintlock/kernel
	make build
	make push

	cd ../base-ubuntu
	make build
	make push

	cd ../../capmvm/kubernetes
	make build
	make push
	```

Your images should end up in your registry. The ones named `flintlock-kernel`
and `capmvm-kubernetes` are the ones you want to set in your capmvm or flintlock
spec.

> Note: These build scripts are not polished, so there may be errors.


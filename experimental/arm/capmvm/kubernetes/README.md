## OS images

The builder provides ARM Operating System images for 4 Kubernetes versions:
`1.21.8`, `1.22.3`, `1.22.8`, and `1.23.5`. The OS is Ubuntu `20.04`.

**Ideally once this dir is merged into non-experimental, we should be publishing
multi-arch images and avoiding duplication.**

These images can be used to provide the root volume for Microvms. They come with
the following pre-installed:
- `containerd`
- `kubelet`
- `kubeadm`
- `kubectl`

The builder can be found at `capmvm/kubernetes`.

The resulting images can be added to Microvm specs like so:

```
ghcr.io/weaveworks-liquidmetal/capmvm-k8s-os-arm:1.21.8
ghcr.io/weaveworks-liquidmetal/capmvm-k8s-os-arm:1.22.3
ghcr.io/weaveworks-liquidmetal/capmvm-k8s-os-arm:1.22.8
ghcr.io/weaveworks-liquidmetal/capmvm-k8s-os-arm:1.23.5
```

For platforms using versions older than [Flintlock `0.5.0`][fl5] and [CAPMVM `0.8.0`][cap8]
us the following legacy images:

```
ghcr.io/weaveworks-liquidmetal/capmvm-kubernetes-arm:X.X.X
```

## Publishing new images

New images will eventually published automatically when merged to main by Github Actions.
For now you have to build them manually as they are still experimental and not
supported.

### Adding new OS versions

- Update the list of `RELEASE_VERSIONS` in `capmvm/kubernetes/Makefile`
- Update any docs (like this one) with new version info
- Update the [Liquid Metal docs][lm-docs] with new version info
- Open a PR with your changes
- Merge once checks and reviews pass

Your new version will be automatically published.

## Local development and custom images

1. Fork this repo, clone your fork
1. `cd` into `experimental/arm/capmvm/kubernetes`
1. Edit the `Dockerfile`
1. Set your image registry `export REGISTRY=docker.io/foobar`
1. If you only want to build one or a subset of kubernetes versions, set this with
	`export RELEASE_VERSIONS=1.23.5`
1. To change the version of Ubuntu, do `export OS_VERSION=22.04`
1. Run `make build` to build the image and `make push` to push the image to your
	registry

[fl5]: https://github.com/weaveworks-liquidmetal/flintlock/releases/tag/v0.5.0
[cap8]: https://github.com/weaveworks-liquidmetal/cluster-api-provider-microvm/releases/tag/v0.8.0
[lm-docs]: https://github.com/weaveworks-liquidmetal/site/blob/main/docs/guides/images.md

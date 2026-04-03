# image-builder
Image building for Liquid Metal.

The images created by this pipeline can be used in [CAPMVM][capmvm] and [Flintlock][flint]
specs to create Microvms.

For kernel image builder instructions, see the doc [here][k-docs].

For OS/k8s image builder instructions, see the doc [here][os-docs].

Experimental images can be found [here][exp].

## Publishing and forks

Workflows push images to the **repository’s GitHub Container Registry** by default:

- **Upstream** (`liquidmetal-dev/image-builder`): images go to `ghcr.io/liquidmetal-dev/...`
- **Forks** (e.g. `my-fork/liquidmetal-image-builder`): images go to `ghcr.io/my-fork/...`

So you can run the same workflows on a fork and publish to your own GHCR without changing the workflow files.

To use a different registry owner (e.g. for the official Liquid Metal org), set the repository variable **`REGISTRY_OWNER`** in the repo’s GitHub settings (e.g. `liquidmetal-dev`). The workflows will then use `ghcr.io/<REGISTRY_OWNER>/...` instead of `ghcr.io/<repository_owner>/...`.

[capmvm]: https://github.com/liquidmetal-dev/cluster-api-provider-microvm
[flint]: https://github.com/liquidmetal-dev/flintlock
[k-docs]: https://github.com/liquidmetal-dev/image-builder/tree/main/kernel
[os-docs]: https://github.com/liquidmetal-dev/image-builder/blob/main/capmvm/kubernetes
[exp]: https://github.com/liquidmetal-dev/image-builder/tree/main/experimental

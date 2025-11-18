# OS images for CAPMVM

The builder provides OS images based on Ubuntu suitable for use with Cluster API Provider Microvm (CAPMVM).

These images can be used to provide the root volume for Microvms. They come with
the following pre-installed:

- `containerd`
- `kubelet`
- `kubeadm`
- `kubectl`

The builder can be found at `capmvm/kubernetes`.

The resulting images can be added to Microvm specs like so:

```text
docker pull ghcr.io/liquidmetal-dev/capmvm-k8s-ubuntu-22.04:1.28.4
```

## Publishing new images

New images are published by invoking the `CAPMVMV - Build and release` GitHub actions workflow.

### Adding new OS versions

- Update the [workflow](../../.github/workflows/capmvm-kubernetes-manual.yml) and to the options for the `ubuntu_version` input.
- Update any docs (like this one) with new version info
- Open a PR with your changes
- Merge once checks and reviews pass

Then ask a maintainer to run the workflow to publish new images.

## Local development and custom images

1. Fork this repo, clone your fork
2. `cd` into `capmvm/kubernetes`
3. Edit the `Dockerfile`
4. Set your image registry `export REGISTRY=docker.io/foobar`
5. Export variables with the Ubuntu, Kubernetes and containerd versions. For example for Kubernetes v1.28.4:

```bash
export UBUNTU_VERSION=22.04
export CONTAINERD_VERSION=1.7.7
export K8S_MAJOR_MINOR=1.28
export K8S_FULL_VERSION=1.28.4
```

6. Run `make build` to build the image and `make push` to push the image to your registry

.PHONY: all buildx build-and-push

DOCKER := docker
BUILD_DATE:=$(shell date --rfc-3339=seconds)
GIT_SHA:=$(shell git rev-parse HEAD)

REGISTRY?=ghcr.io/liquidmetal-dev
IMAGE_NAME?=$(REGISTRY)/capmvm-k8s-ubuntu-$(UBUNTU_VERSION)-arm64
CONTAINERD_VERSION?=1.7.27
UBUNTU_VERSION?=22.04

# Single version to build (e.g. 1.28.4); set via workflow or: RELEASE_VERSIONS=1.28.4 make build-and-push
RELEASE_VERSIONS?=1.21.8 1.22.3 1.22.8 1.23.5

build-and-push: $(addprefix build-and-push-,$(RELEASE_VERSIONS))
build-and-push-%:
	$(DOCKER) buildx build --platform linux/arm64 -t $(IMAGE_NAME):$*  \
		--build-arg KUBERNETES_MAJOR_MINOR=$(shell echo $* | cut -d. -f1,2) \
		--build-arg KUBERNETES_FULL_VERSION=$* \
		--build-arg CONTAINERD_VERSION=$(CONTAINERD_VERSION) \
		--build-arg OS_VERSION=$(UBUNTU_VERSION) \
		--label "org.opencontainers.image.created"="$(BUILD_DATE)" \
		--label "org.opencontainers.image.authors"="Liquid Metal Authors" \
		--label "org.opencontainers.image.url"="https://github.com/liquidmetal-dev/image-builder" \
		--label "org.opencontainers.image.source"="https://github.com/liquidmetal-dev/image-builder/tree/main/capmvm/kubernetes" \
		--label "org.opencontainers.image.revision"="$(GIT_SHA)" \
		--label "org.opencontainers.image.vendor"="Weaveworks" \
		--label "org.opencontainers.image.licenses"="MPL-2.0" \
		--label "org.opencontainers.image.title"="Cluster API Provider Microvm Kubernetes v$*" \
		--label "org.opencontainers.image.description"="A image to use for a microvm volume when creating a cluster using Cluster API Provider Microvm" \
		--label "works.weave.liquidmetal.kubernetes"="$*" \
		--label "works.weave.liquidmetal.containerd"="$(CONTAINERD_VERSION)" \
		--push .

buildx:
	$(DOCKER) buildx create --name armbuilder --use --bootstrap

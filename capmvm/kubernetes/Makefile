DOCKER := docker
BUILD_DATE:=$(shell date --rfc-3339=seconds)
GIT_SHA:=$(shell git rev-parse HEAD)

REGISTRY?=ghcr.io/liquidmetal-dev
CONTAINERD_VERSION?=1.7.27
K8S_MAJOR_MINOR?=1.30
K8S_FULL_VERSION?=$(K8S_MAJOR_MINOR).14
UBUNTU_VERSION?=22.04
IMAGE_NAME?=$(REGISTRY)/capmvm-k8s-ubuntu-$(UBUNTU_VERSION)
IMAGE?=$(IMAGE_NAME):$(K8S_FULL_VERSION)

build: 
	$(DOCKER) build -t $(IMAGE)  \
		--build-arg KUBERNETES_FULL_VERSION=$(K8S_FULL_VERSION) \
		--build-arg KUBERNETES_MAJOR_MINOR=$(K8S_MAJOR_MINOR) \
		--build-arg CONTAINERD_VERSION=$(CONTAINERD_VERSION) \
		--build-arg OS_VERSION=$(UBUNTU_VERSION) \
		--label "org.opencontainers.image.created"="$(BUILD_DATE)" \
		--label "org.opencontainers.image.authors"="Liquid Metal Authors" \
		--label "org.opencontainers.image.url"="https://github.com/liquidmetal-dev/image-builder" \
		--label "org.opencontainers.image.source"="https://github.com/liquidmetal-dev/image-builder/tree/main/capmvm/kubernetes" \
		--label "org.opencontainers.image.revision"="$(GIT_SHA)" \
		--label "org.opencontainers.image.vendor"="Liquid Metal Project" \
		--label "org.opencontainers.image.licenses"="MPL-2.0" \
		--label "org.opencontainers.image.title"="Cluster API Provider Microvm Kubernetes v$(K8S_FULL_VERSION)" \
		--label "org.opencontainers.image.description"="A image to use for a microvm volume when creating a cluster using Cluster API Provider Microvm" \
		--label "dev.liquidmetal.kubernetes"="$(K8S_FULL_VERSION)" \
		--label "dev.liquidmetal.containerd"="$(CONTAINERD_VERSION)" \
		.

push:
	$(DOCKER) push $(IMAGE)

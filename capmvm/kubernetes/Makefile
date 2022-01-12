DOCKER := docker

REGISTRY?=ghcr.io/weaveworks
IMAGE_NAME?=$(REGISTRY)/capmvm-kubernetes
RELEASE?=1.21.8 # RELEASE follows the kubernetes release
CONTAINERD_VERSION?=1.5.9
TAG?=$(shell git rev-parse --short HEAD)

build:
	$(DOCKER) build -t $(IMAGE_NAME):$(RELEASE) \
		--build-arg KUBERNETES_VERSION=$(RELEASE) \
		--build-arg CONTAINERD_VERSION=$(CONTAINERD_VERSION) .
	$(DOCKER) tag $(IMAGE_NAME):$(RELEASE) $(IMAGE_NAME):$(TAG)

push:
	$(DOCKER) push $(IMAGE_NAME):$(RELEASE)
	$(DOCKER) push $(IMAGE_NAME):$(TAG)
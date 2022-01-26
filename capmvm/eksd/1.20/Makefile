DOCKER := docker

REGISTRY?=ghcr.io/weaveworks
IMAGE_NAME?=$(REGISTRY)/capmvm-kubernetes
RELEASE?=v1.20.11-eks-1-20-10# RELEASE follows the EKS-D release
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
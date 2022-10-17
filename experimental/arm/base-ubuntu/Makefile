DOCKER := docker

REGISTRY?=ghcr.io/weaveworks-liquidmetal
IMAGE_NAME?=$(REGISTRY)/flintlock-ubuntu-base-arm
RELEASE?=20.04
IS_LATEST?=
TAG?=$(shell git rev-parse --short HEAD)

build-and-push: buildx
	$(DOCKER) buildx build --platform arm64 -t $(IMAGE_NAME):$(RELEASE) \
		--build-arg OS_VERSION=$(RELEASE) \
		--build-arg KERNEL_REGISTRY=$(REGISTRY) --push .

buildx:
	$(DOCKER) buildx create --name armbuilder --use --bootstrap

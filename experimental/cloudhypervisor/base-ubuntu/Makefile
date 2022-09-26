DOCKER := docker
BUILD_DATE:=$(shell date --rfc-3339=seconds)
GIT_SHA:=$(shell git rev-parse HEAD)

REGISTRY?=ghcr.io/weaveworks-liquidmetal
IMAGE_NAME?=$(REGISTRY)/flintlock-ubuntu-base-cloudhypervisor
RELEASE?=20.04
IS_LATEST?=
TAG?=$(shell git rev-parse --short HEAD)

build:
	$(DOCKER) build -t $(IMAGE_NAME):$(RELEASE) \
		--build-arg OS_VERSION=$(RELEASE) \
		--build-arg KERNEL_REGISTRY=$(REGISTRY) \
		--label "org.opencontainers.image.created"="$(BUILD_DATE)" \
		--label "org.opencontainers.image.authors"="Liquid Metal Authors" \
		--label "org.opencontainers.image.url"="https://github.com/weaveworks-liquidmetal/image-builder" \
		--label "org.opencontainers.image.source"="https://github.com/weaveworks-liquidmetal/image-builder" \
		--label "org.opencontainers.image.revision"="$(GIT_SHA)" \
		--label "org.opencontainers.image.vendor"="Weaveworks" \
		--label "org.opencontainers.image.licenses"="MPL-2.0" \
		--label "org.opencontainers.image.title"="Flintlock Ubuntu Base Image" \
		--label "org.opencontainers.image.description"="A image to use for a microvm volume when creating a microvm using Flintlock" \
		.
	$(DOCKER) tag $(IMAGE_NAME):$(RELEASE) $(IMAGE_NAME):$(TAG)

push:
	$(DOCKER) push $(IMAGE_NAME):$(RELEASE)
	$(DOCKER) push $(IMAGE_NAME):$(TAG)
DOCKER := docker

REGISTRY?=ghcr.io/weaveworks-liquidmetal
IMAGE_NAME?=$(REGISTRY)/flintlock-ubuntu-base
RELEASE?=20.04
IS_LATEST?=
TAG?=$(shell git rev-parse --short HEAD)

build:
	$(DOCKER) build -t $(IMAGE_NAME):$(RELEASE) \
		--build-arg OS_VERSION=$(RELEASE) .
	$(DOCKER) tag $(IMAGE_NAME):$(RELEASE) $(IMAGE_NAME):$(TAG)

push:
	$(DOCKER) push $(IMAGE_NAME):$(RELEASE)
	$(DOCKER) push $(IMAGE_NAME):$(TAG)
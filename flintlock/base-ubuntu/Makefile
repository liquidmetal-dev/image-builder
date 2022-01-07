DOCKER := docker

REGISTRY?=ghcr.io/weaveworks
IMAGE_NAME?=$(REGISTRY)/flintlock-ubuntu-base
RELEASE?=20.04
IS_LATEST?=
TAG?=$(shell git rev-parse --short HEAD)

build:
	$(DOCKER) build -t $(IMAGE_NAME):$(RELEASE) \
		--build-arg OS_VERSION=$(RELEASE) .
	$(DOCKER) tag $(IMAGE_NAME):$(RELEASE) $(IMAGE_NAME):$(TAG)
ifeq ($(IS_LATEST),true)
	$(DOCKER) tag $(IMAGE_NAME):$(RELEASE) $(IMAGE_NAME):latest
endif

push:
	$(DOCKER) push $(IMAGE_NAME):$(RELEASE)
	$(DOCKER) push $(IMAGE_NAME):$(TAG)
ifeq ($(IS_LATEST),true)
	$(DOCKER) push $(IMAGE_NAME):latest
endif
DOCKER := docker

REGISTRY?=gcr.io/weaveworks
IMAGE_NAME?=$(REGISTRY)/flintlock-ubuntu-base
RELEASE?=20.04
IS_LATEST?=

build:
	$(DOCKER) build -t $(IMAGE_NAME):$(RELEASE) \
		--build-arg OS_VERSION=$(RELEASE) .
ifeq ($(IS_LATEST),true)
	$(DOCKER) tag $(IMAGE_NAME) latest
endif

push:
	$(DOCKER) push $(IMAGE_NAME):$(RELEASE)
ifeq ($(IS_LATEST),true)
	$(DOCKER) push $(IMAGE_NAME):latest
endif
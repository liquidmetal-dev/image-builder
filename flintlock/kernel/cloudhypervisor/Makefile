DOCKER := docker

REGISTRY?=ghcr.io/weaveworks
IMAGE_NAME?=$(REGISTRY)/flintlock-kernel-cloudhypervisor
BUILDER_IMAGE_NAME?=flintlock-kernel-builder-ch
CH_VERSION ?= 23.0
KERNEL_VERSIONS ?= 5.12

all: build

kernel-builder:
	$(DOCKER) build -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile .

build: $(addprefix build-,$(KERNEL_VERSIONS))
build-%: kernel-builder
	$(DOCKER) build -t $(IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg CH_VERSION=$(CH_VERSION) \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) .

push: $(addprefix push-,$(KERNEL_VERSIONS))
push-%:
	$(DOCKER) push $(IMAGE_NAME):$*

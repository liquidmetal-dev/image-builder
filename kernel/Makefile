.PHONY: all builder build push

DOCKER := docker

REGISTRY?=ghcr.io/weaveworks-liquidmetal

BUILDER_IMAGE_NAME?=$(REGISTRY)/kernel-builder
BASE_IMAGE_NAME?=$(REGISTRY)/flintlock-kernel
BIN_IMAGE_NAME?=$(REGISTRY)/kernel-bin
MOD_IMAGE_NAME?=$(REGISTRY)/kernel-modules

KERNEL_VERSIONS ?= 4.19.215 5.10.77 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!

all: build

builder:
	$(DOCKER) build -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile .

build: $(addprefix build-,$(KERNEL_VERSIONS))
build-%: builder
	$(MAKE) base-$*
	$(MAKE) binary-$*
	$(MAKE) modules-$*
base-%:
	$(DOCKER) build -t $(BASE_IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		-f base/Dockerfile .
binary-%:
	$(DOCKER) build -t $(BIN_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(BASE_IMAGE_NAME):$* \
		-f bin/Dockerfile .
modules-%:
	$(DOCKER) build -t $(MOD_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(BASE_IMAGE_NAME):$* \
		-f modules/Dockerfile .

push: $(addprefix push-,$(KERNEL_VERSIONS))
push-%:
	$(DOCKER) push $(BASE_IMAGE_NAME):$*
	$(DOCKER) push $(BIN_IMAGE_NAME):$*
	$(DOCKER) push $(MOD_IMAGE_NAME):$*

.PHONY: all builder build-and-push-fc

DOCKER := docker

REGISTRY?=ghcr.io/weaveworks-liquidmetal

BUILDER_IMAGE_NAME?=$(REGISTRY)/kernel-builder-arm

FC_BASE_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-arm
FC_BIN_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-bin-arm
FC_MOD_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-modules-arm

FC_KERNEL_VERSIONS ?= 5.10.77 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!

##@ Build

all: build-and-push-fc ## Build all Firecracker and images

builder: ## Build the builder image
	$(DOCKER) buildx build --platform linux/arm64 -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile --push .

build-and-push-fc: $(addprefix build-fc-,$(FC_KERNEL_VERSIONS)) ## Build all Firecracker images
build-fc-%: builder
	$(MAKE) base-fc-$*
	$(MAKE) binary-fc-$*
	$(MAKE) modules-fc-$*
base-fc-%: ## Build the Firecracker base image
	$(DOCKER) buildx build --platform linux/arm64 -t $(FC_BASE_IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		--push \
		-f firecracker/base/Dockerfile .
binary-fc-%: ## Build the Firecracker binary image
	$(DOCKER) buildx build --platform linux/arm64 -t $(FC_BIN_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(FC_BASE_IMAGE_NAME):$* \
		--push \
		-f bin/Dockerfile .
modules-fc-%: ## Build the Firecracker modules image
	$(DOCKER) buildx build --platform linux/arm64 -t $(FC_MOD_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(FC_BASE_IMAGE_NAME):$* \
		--push \
		-f modules/Dockerfile .

buildx:
	$(DOCKER) buildx create --name armbuilder --use --bootstrap

##@ Utility

.PHONY: help
help: ## Display this help.
	@awk 'BEGIN {FS = ":.*##"; printf "\nUsage:\n  make \033[36m<target>\033[0m\n"} /^[a-zA-Z_0-9-]+:.*?##/ { printf "  \033[36m%-15s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$0, 5) } ' $(MAKEFILE_LIST)

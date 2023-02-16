.PHONY: all builder build-fc build-ch push-fc push-ch

DOCKER := docker

REGISTRY?=ghcr.io/weaveworks-liquidmetal

BUILDER_IMAGE_NAME?=$(REGISTRY)/kernel-builder

FC_BASE_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel
FC_BIN_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-bin
FC_MOD_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-modules

CH_BASE_IMAGE_NAME?=$(REGISTRY)/cloudhypervisor-kernel
CH_BIN_IMAGE_NAME?=$(REGISTRY)/cloudhypervisor-kernel-bin
CH_MOD_IMAGE_NAME?=$(REGISTRY)/cloudhypervisor-kernel-modules

FC_KERNEL_VERSIONS ?= 4.19.215 5.10.77 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!
CH_KERNEL_VERSIONS ?= 5.12 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!

##@ Build

all: build-fc build-ch ## Build all Firecracker and Cloud Hypervisor images

builder: ## Build the builder image
	$(DOCKER) build -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile .

build-fc: $(addprefix build-fc-,$(FC_KERNEL_VERSIONS)) ## Build all Firecracker images
build-fc-%: builder
	$(MAKE) base-fc-$*
	$(MAKE) binary-fc-$*
	$(MAKE) modules-fc-$*
base-fc-%: ## Build the Firecracker base image
	$(DOCKER) build -t $(FC_BASE_IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		-f firecracker/base/Dockerfile .
binary-fc-%: ## Build the Firecracker binary image
	$(DOCKER) build -t $(FC_BIN_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(FC_BASE_IMAGE_NAME):$* \
		-f bin/Dockerfile .
modules-fc-%: ## Build the Firecracker modules image
	$(DOCKER) build -t $(FC_MOD_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(FC_BASE_IMAGE_NAME):$* \
		-f modules/Dockerfile .

build-ch: $(addprefix build-ch-,$(CH_KERNEL_VERSIONS)) ## Build all Cloud Hypervisor images
build-ch-%: builder
	$(MAKE) base-ch-$*
	$(MAKE) binary-ch-$*
	$(MAKE) modules-ch-$*
base-ch-%: ## Build the Cloud Hypervisor base image
	$(DOCKER) build -t $(CH_BASE_IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		-f cloud-hypervisor/base/Dockerfile .
binary-ch-%: ## Build the Cloud Hypervisor binary image
	$(DOCKER) build -t $(CH_BIN_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(CH_BASE_IMAGE_NAME):$* \
		-f bin/Dockerfile .
modules-ch-%: ## Build the Cloud Hypervisor modules image
	$(DOCKER) build -t $(CH_MOD_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(CH_BASE_IMAGE_NAME):$* \
		-f modules/Dockerfile .

##@ Push

push-fc: $(addprefix push-fc-,$(FC_KERNEL_VERSIONS))
push-fc-%: ## Push all Firecracker images
	$(DOCKER) push $(FC_BASE_IMAGE_NAME):$*
	$(DOCKER) push $(FC_BIN_IMAGE_NAME):$*
	$(DOCKER) push $(FC_MOD_IMAGE_NAME):$*

push-ch: $(addprefix push-ch-,$(CH_KERNEL_VERSIONS))
push-ch-%: ## Push all Cloud Hypervisor images
	$(DOCKER) push $(CH_BASE_IMAGE_NAME):$*
	$(DOCKER) push $(CH_BIN_IMAGE_NAME):$*
	$(DOCKER) push $(CH_MOD_IMAGE_NAME):$*

##@ Utility

.PHONY: help
help: ## Display this help.
	@awk 'BEGIN {FS = ":.*##"; printf "\nUsage:\n  make \033[36m<target>\033[0m\n"} /^[a-zA-Z_0-9-]+:.*?##/ { printf "  \033[36m%-15s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$0, 5) } ' $(MAKEFILE_LIST)

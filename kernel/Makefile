.PHONY: all builder build-fc build-ch push-fc push-ch

DOCKER := docker

# Architecture: amd64 or arm64. Drives platform, image name suffix, and config paths.
ARCH ?= amd64
PLATFORM := linux/$(ARCH)

REGISTRY?=ghcr.io/liquidmetal-dev

# Image names include -$(ARCH) suffix for multi-arch support.
BUILDER_IMAGE_NAME?=$(REGISTRY)/kernel-builder-$(ARCH)
FC_BASE_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-$(ARCH)
FC_BIN_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-bin-$(ARCH)
FC_MOD_IMAGE_NAME?=$(REGISTRY)/firecracker-kernel-modules-$(ARCH)
CH_BASE_IMAGE_NAME?=$(REGISTRY)/cloudhypervisor-kernel-$(ARCH)
CH_BIN_IMAGE_NAME?=$(REGISTRY)/cloudhypervisor-kernel-bin-$(ARCH)
CH_MOD_IMAGE_NAME?=$(REGISTRY)/cloudhypervisor-kernel-modules-$(ARCH)

# Config filename suffix per arch: amd64 -> x86_64, arm64 -> arm64 (for linux-<suffix>-<version>.config).
ifeq ($(ARCH),amd64)
FC_CONFIG_SUFFIX := x86_64
else
FC_CONFIG_SUFFIX := arm64
endif

FC_KERNEL_VERSIONS ?= 4.19.215 5.10.77 6.1.102 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!
CH_KERNEL_VERSIONS ?= 5.12 5.15.12 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!

# For arm64 we use buildx (required for cross-build); PUSH can be set to --push in CI.
PUSH ?=
ifeq ($(ARCH),arm64)
DOCKER_BUILD_CMD := $(DOCKER) buildx build
DOCKER_BUILD_OPTS := --platform $(PLATFORM) $(PUSH)
else
DOCKER_BUILD_CMD := $(DOCKER) build
DOCKER_BUILD_OPTS := --platform $(PLATFORM)
endif

##@ Build

all: build-fc build-ch ## Build all Firecracker and Cloud Hypervisor images for current ARCH

builder: ## Build the builder image for current ARCH
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile .

build-fc: $(addprefix build-fc-,$(FC_KERNEL_VERSIONS)) ## Build all Firecracker images for current ARCH
build-fc-%: builder
	$(MAKE) base-fc-$*
	$(MAKE) binary-fc-$*
	$(MAKE) modules-fc-$*
base-fc-%: ## Build the Firecracker base image for current ARCH
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(FC_BASE_IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg ARCH=$(ARCH) \
		--build-arg CONFIG_FILE=linux-$(FC_CONFIG_SUFFIX)-$*.config \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		-f firecracker/base/Dockerfile .
binary-fc-%: ## Build the Firecracker binary image for current ARCH
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(FC_BIN_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(FC_BASE_IMAGE_NAME):$* \
		-f bin/Dockerfile .
modules-fc-%: ## Build the Firecracker modules image for current ARCH
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(FC_MOD_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(FC_BASE_IMAGE_NAME):$* \
		-f modules/Dockerfile .

build-ch: $(addprefix build-ch-,$(CH_KERNEL_VERSIONS)) ## Build all Cloud Hypervisor images (amd64 only for now)
build-ch-%: builder
	$(MAKE) base-ch-$*
	$(MAKE) binary-ch-$*
	$(MAKE) modules-ch-$*
base-ch-%: ## Build the Cloud Hypervisor base image
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(CH_BASE_IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg ARCH=$(ARCH) \
		--build-arg CONFIG_FILE=linux-x86_64-$*.config \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		-f cloud-hypervisor/base/Dockerfile .
binary-ch-%: ## Build the Cloud Hypervisor binary image
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(CH_BIN_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(CH_BASE_IMAGE_NAME):$* \
		-f bin/Dockerfile .
modules-ch-%: ## Build the Cloud Hypervisor modules image
	$(DOCKER_BUILD_CMD) $(DOCKER_BUILD_OPTS) -t $(CH_MOD_IMAGE_NAME):$* \
		--build-arg IMAGE_NAME=$(CH_BASE_IMAGE_NAME):$* \
		-f modules/Dockerfile .

##@ Push

push-fc: $(addprefix push-fc-,$(FC_KERNEL_VERSIONS))
push-fc-%: ## Push Firecracker images for current ARCH
	$(DOCKER) push $(FC_BASE_IMAGE_NAME):$*
	$(DOCKER) push $(FC_BIN_IMAGE_NAME):$*
	$(DOCKER) push $(FC_MOD_IMAGE_NAME):$*

push-ch: $(addprefix push-ch-,$(CH_KERNEL_VERSIONS))
push-ch-%: ## Push Cloud Hypervisor images for current ARCH
	$(DOCKER) push $(CH_BASE_IMAGE_NAME):$*
	$(DOCKER) push $(CH_BIN_IMAGE_NAME):$*
	$(DOCKER) push $(CH_MOD_IMAGE_NAME):$*

##@ Utility

.PHONY: help
help: ## Display this help.
	@awk 'BEGIN {FS = ":.*##"; printf "\nUsage:\n  make \033[36m<target>\033[0m [ARCH=amd64|arm64]\n"} /^[a-zA-Z_0-9-]+:.*?##/ { printf "  \033[36m%-15s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$0, 5) } ' $(MAKEFILE_LIST)

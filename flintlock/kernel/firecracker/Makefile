DOCKER := docker

REGISTRY?=ghcr.io/weaveworks
IMAGE_NAME?=$(REGISTRY)/flintlock-kernel-firecracker
BUILDER_IMAGE_NAME?=flintlock-kernel-builder-firecracker
KERNEL_VERSIONS ?= 4.19.215 5.10.77 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!

all: build

kernel-builder:
	$(DOCKER) build -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile .

build: $(addprefix build-,$(KERNEL_VERSIONS))
build-%: kernel-builder
	$(DOCKER) build -t $(IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) .

push: $(addprefix push-,$(KERNEL_VERSIONS))
push-%:
	$(DOCKER) push $(IMAGE_NAME):$*

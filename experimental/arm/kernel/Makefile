DOCKER := docker

REGISTRY?=ghcr.io/weaveworks-liquidmetal
IMAGE_NAME?=$(REGISTRY)/flintlock-kernel-arm
BUILDER_IMAGE_NAME?=$(REGISTRY)/flintlock-kernel-builder-arm
KERNEL_VERSIONS ?= 5.10.77 # If you update this list, please remember to update the matrix in the kernel-images.yml github action!

all: build-and-push

kernel-builder: buildx
	$(DOCKER) buildx build --platform linux/arm64 -t $(BUILDER_IMAGE_NAME):dev \
		-f builder/Dockerfile --push .

build-and-push: $(addprefix build-and-push-,$(KERNEL_VERSIONS))
build-and-push-%: kernel-builder
	$(DOCKER) buildx build --platform linux/arm64 -t $(IMAGE_NAME):$* \
		--build-arg KERNEL_VERSION=$* \
		--build-arg IMAGE_NAME=$(BUILDER_IMAGE_NAME) \
		--push .

buildx:
	$(DOCKER) buildx create --name armbuilder --use --bootstrap

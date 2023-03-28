.PHONY: all buildx build-and-push

DOCKER := docker
BUILD_DATE:=$(shell date --rfc-3339=seconds)
GIT_SHA:=$(shell git rev-parse HEAD)

REGISTRY?=ghcr.io/weaveworks-liquidmetal
IMAGE_NAME?=$(REGISTRY)/action-runner-arm
TAG?=$(shell git rev-parse --short HEAD)

RUNNER_VERSION?=2.303.0

# TODO have multiple OS opts

build-and-push:
	$(DOCKER) buildx build --platform linux/arm64 -t $(IMAGE_NAME):$(RUNNER_VERSION)  \
		--build-arg RUNNER_VERSION=$(RUNNER_VERSION) \
		--push .

buildx:
	$(DOCKER) buildx create --name armbuilder --use --bootstrap

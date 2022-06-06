DOCKER := docker
BUILD_DATE:=$(shell date --rfc-3339=seconds)
GIT_SHA:=$(shell git rev-parse HEAD)

REGISTRY?=ghcr.io/weaveworks-liquidmetal
IMAGE_NAME?=$(REGISTRY)/flintlock-ubuntu-perftest
TAG?=$(shell git rev-parse --short HEAD)
IPERF3_VERSION?=3.9-1

build:
	$(DOCKER) build -t $(IMAGE_NAME):$(TAG)  \
		--build-arg IPERF3_VERSION=$(IPERF3_VERSION) \
		--label "org.opencontainers.image.created"="$(BUILD_DATE)" \
		--label "org.opencontainers.image.authors"="Liquid Metal Authors" \
		--label "org.opencontainers.image.url"="https://github.com/weaveworks-liquidmetal/image-builder" \
		--label "org.opencontainers.image.source"="https://github.com/weaveworks-liquidmetal/image-builder/tree/main/flintlock/perf-test" \
		--label "org.opencontainers.image.revision"="$(GIT_SHA)" \
		--label "org.opencontainers.image.vendor"="Weaveworks" \
		--label "org.opencontainers.image.licenses"="MPL-2.0" \
		--label "org.opencontainers.image.title"="Ubuntu based perf testing image" \
		--label "org.opencontainers.image.description"="A image to use for perf testing microvms" \
		--label "works.weave.liquidmetal.iperf3"="$(IPERF3_VERSION)" \
		.

push:
	$(DOCKER) push $(IMAGE_NAME):$(TAG)

VERSIONS?=1.23.10 # VERSIONS follows the kubernetes release
# VERSIONS?=1.21.8 1.22.3 1.22.8 1.23.5 # VERSIONS follows kubernetes release versions

build: $(addprefix build-,$(VERSIONS))
build-%:
	$(DOCKER) build -t $(IMAGE_NAME):$*  \
		--build-arg BASE_IMAGE=$(BASE_IMAGE):v$* \ # TODO remove the v here and from the published bases
		--label "org.opencontainers.image.created"="$(BUILD_DATE)" \
		--label "org.opencontainers.image.authors"="Liquid Metal Authors" \
		--label "org.opencontainers.image.url"="https://github.com/weaveworks-liquidmetal/image-builder" \
		--label "org.opencontainers.image.source"="https://github.com/weaveworks-liquidmetal/image-builder/tree/main/capmvm/kubernetes" \
		--label "org.opencontainers.image.revision"="$(GIT_SHA)" \
		--label "org.opencontainers.image.vendor"="Weaveworks" \
		--label "org.opencontainers.image.licenses"="MPL-2.0" \
		--label "org.opencontainers.image.title"="Cluster API Provider Microvm Kubernetes v$*" \
		--label "org.opencontainers.image.description"="A image to use for a microvm volume when creating a cluster using Cluster API Provider Microvm" \
		--label "works.weave.liquidmetal.kubernetes"="$*" \
		.

push: $(addprefix push-,$(VERSIONS))
push-%:
	$(DOCKER) push $(IMAGE_NAME):$*

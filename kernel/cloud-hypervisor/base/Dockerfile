# Default matches kernel/Makefile BUILDER_IMAGE_NAME for amd64 (CH is amd64-only for now)
ARG IMAGE_NAME=ghcr.io/liquidmetal-dev/kernel-builder-amd64
FROM ${IMAGE_NAME}:dev AS builder

ENV SRC_DIR=/usr/src            \
    DIST_DIR=/dist              \
    LINUX_DIR=/usr/src/linux    \
    LINUX_REPO_URL=https://github.com/cloud-hypervisor/linux.git

ARG KERNEL_VERSION
ARG ARCH=amd64
ARG CONFIG_FILE

RUN mkdir -p ${SRC_DIR} ${DIST_DIR} && \
    git clone --depth 1 --branch ch-${KERNEL_VERSION} ${LINUX_REPO_URL} ${LINUX_DIR} && \
    cd ${LINUX_DIR}

WORKDIR ${LINUX_DIR}

COPY cloud-hypervisor/configs/${ARCH}/${CONFIG_FILE} .config

RUN make LOCALVERSION= olddefconfig
RUN make LOCALVERSION= -j `nproc` all
RUN make LOCALVERSION= modules_install

RUN cp arch/x86/boot/compressed/vmlinux.bin /boot/vmlinux.bin && \
    cp .config /boot/config-${KERNEL_VERSION}

FROM scratch
COPY --from=builder /boot /boot
COPY --from=builder /lib/modules /lib/modules

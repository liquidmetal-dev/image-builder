ARG IMAGE_NAME
FROM ${IMAGE_NAME}:dev AS builder

ENV SRC_DIR=/usr/src            \
    DIST_DIR=/dist              \
    LINUX_DIR=/usr/src/linux    \
    LINUX_REPO_URL=git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git

ARG KERNEL_VERSION

RUN mkdir -p ${SRC_DIR} ${DIST_DIR} && \
    git clone --depth 1 --branch v${KERNEL_VERSION} ${LINUX_REPO_URL} ${LINUX_DIR} && \
    cd ${LINUX_DIR}

WORKDIR ${LINUX_DIR}

COPY firecracker/configs/linux-arm64-${KERNEL_VERSION}.config .config

RUN make LOCALVERSION= olddefconfig
# -j32 tells the compiler to use 32 threads, if 32 threads are not available it will use as many as it can up to 32
RUN make LOCALVERSION= -j32
RUN make LOCALVERSION= modules_install

RUN make Image

RUN cp ./arch/arm64/boot/Image /boot/image && \
    cp .config /boot/config-${KERNEL_VERSION}

FROM scratch
COPY --from=builder /boot /boot
COPY --from=builder /lib/modules /lib/modules

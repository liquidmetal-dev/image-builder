ARG OS_VERSION
ARG KERNEL_REGISTRY

FROM ${KERNEL_REGISTRY}/flintlock-kernel:5.10.77 as kernel51077
FROM ${KERNEL_REGISTRY}/flintlock-kernel:4.19.215 as kernel419215

FROM ubuntu:${OS_VERSION}

RUN apt-get update && apt-get install -y \
        systemd \
        udev \
        kmod \
        dbus \
        iproute2 \
        iputils-ping \
        net-tools \
        openssh-server \
        dnsutils \
        sudo \
        curl \
        cloud-init \
        linux-image-virtual  \
        less \
        wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    sed -i -e 's/^AcceptEnv LANG LC_\*$/#AcceptEnv LANG LC_*/' /etc/ssh/sshd_config && \
    mkdir -p /lib/modules

# Add modules from the 2 kernel images we support
COPY --from=kernel51077 /lib /lib
COPY --from=kernel419215 /lib /lib

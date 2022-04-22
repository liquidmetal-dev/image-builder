ARG OS_VERSION
ARG KERNEL_REGISTRY

FROM ${KERNEL_REGISTRY}/flintlock-kernel-cloudhypervisor:5.12 as kernel512

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
    mkdir -p /lib/modules && \
    echo -e 'LANG=en_US.UTF-8\nLC_ALL=en_US.UTF-8' > /etc/default/locale

 


COPY --from=kernel512 /lib /lib
COPY --from=kernel512 /boot/config-5.12 /boot/config-5.12.0

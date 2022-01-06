ARG OS_VERSION
FROM ubuntu:${OS_VERSION}

RUN apt-get update && apt-get install -y \
        systemd \
        udev \
        kmod \
        dbus \
        iproute2 \
        iputils-ping \
        net-tools \
        cloud-init \
        sudo \
        curl \
        wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN echo "" > /etc/machine-id
RUN echo "root:root" | chpasswd
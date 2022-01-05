ARG OS_VERSION
FROM ubuntu:${OS_VERSION}

RUN apt-get update && apt-get install -y \
        udev \
        systemd \
        systemd-sysv \
        iproute2 \
        net-tools \
        sudo \
        openssh-server \
        curl \
        wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
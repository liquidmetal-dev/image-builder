ARG OS_VERSION=20.04
FROM ubuntu:${OS_VERSION}

ARG ARCH="arm64"
ARG RUNNER_VERSION="2.303.0"

ENV USER=ubuntu
ENV WORK_DIR="/home/$USER/actions-runner"

RUN apt-get update && apt-get install -y \
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
        udev \
        wget \
        apt-transport-https \
        ca-certificates \
        gnupg2 \
        software-properties-common \
        libseccomp2 \
        systemd \
        libyaml-dev \
        jq && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    sed -i -e 's/^AcceptEnv LANG LC_\*$/#AcceptEnv LANG LC_*/' /etc/ssh/sshd_config

RUN adduser --disabled-password --gecos "" ${USER} \
        && usermod -aG sudo ${USER} \
        && passwd -d ${USER}

RUN mkdir -p ${WORK_DIR} && chown ${USER}:${USER} ${WORK_DIR}

WORKDIR ${WORK_DIR}
USER ${USER}

RUN curl -fLo runner.tar.gz https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/actions-runner-linux-${ARCH}-${RUNNER_VERSION}.tar.gz \
        && tar xzf ./runner.tar.gz \
        && rm runner.tar.gz \
        && sudo ./bin/installdependencies.sh

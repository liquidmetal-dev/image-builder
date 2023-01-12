ARG OS_VERSION=20.04
FROM ubuntu:${OS_VERSION}

ARG ARCH="amd64"
ARG CONTAINERD_VERSION=1.5.9
# Kubernetes version will be used to keep the kubeadm, kubelet, and kubectl version consistent
ARG KUBERNETES_VERSION=1.21.8

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
        jq && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    sed -i -e 's/^AcceptEnv LANG LC_\*$/#AcceptEnv LANG LC_*/' /etc/ssh/sshd_config

#### Let iptables see bridged traffic ###
RUN echo "overlay \n\
br_netfilter" >> /etc/modules-load.d/containerd.conf

# Setup required sysctl params, these persist across reboots.
RUN echo "net.bridge.bridge-nf-call-iptables  = 1 \n\
net.bridge.bridge-nf-call-ip6tables = 1 \n\
net.ipv4.ip_forward                 = 1 \n\
net.ipv6.conf.all.forwarding = 1 \n\
net.ipv6.conf.all.disable_ipv6 = 0 \n\
net.ipv4.tcp_congestion_control = bbr \n\
vm.overcommit_memory = 1 \n\
kernel.panic = 10 \n\
net.ipv4.conf.all.rp_filter = 1 \n\
kernel.panic_on_oops = 1" >> /etc/sysctl.d/99-kubernetes-cri.conf

# Apply sysctl params without reboot
RUN sysctl --system

# Install Containerd
RUN wget https://github.com/containerd/containerd/releases/download/v${CONTAINERD_VERSION}/cri-containerd-cni-${CONTAINERD_VERSION}-linux-${ARCH}.tar.gz
RUN tar --no-overwrite-dir -C / -xzf cri-containerd-cni-${CONTAINERD_VERSION}-linux-${ARCH}.tar.gz && rm -f cri-containerd-cni-${CONTAINERD_VERSION}-linux-${ARCH}.tar.gz

### Add apt repos
# Kubeadm, Kubelet, and Kubectl
RUN curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
RUN echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

RUN apt-get update && apt-get install -y \
        kubeadm=${KUBERNETES_VERSION}-00 \
        kubelet=${KUBERNETES_VERSION}-00 \
        kubectl=${KUBERNETES_VERSION}-00 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    apt-mark hold kubelet kubeadm kubectl

RUN mkdir -p /etc/containerd && containerd config default > /etc/containerd/config.toml
RUN rm /etc/cni/net.d/10-containerd-net.conflist
RUN systemctl enable containerd

RUN echo '[Service] \n\
Environment="KUBELET_EXTRA_ARGS=--container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock"' \
    >> /etc/systemd/system/kubelet.service.d/0-containerd.conf

RUN systemctl enable kubelet

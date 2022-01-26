FROM ghcr.io/weaveworks/flintlock-ubuntu-base:20.04


ARG ARCH="amd64"
ARG CONTAINERD_VERSION=1.5.9

RUN apt-get update && apt-get install -y \
        wget \
        apt-transport-https \
        ca-certificates \
        gnupg2 \
        software-properties-common \
        libseccomp2 \
        ebtables \
        ethtool \
        socat \
        conntrack \
        iptables \
        locales \
        jq

#### Let iptables see bridged traffic ###
RUN echo "overlay \n\
br_netfilter" >> /etc/modules-load.d/containerd.conf

# Setup required sysctl params, these persist across reboots.
RUN echo "net.bridge.bridge-nf-call-iptables  = 1 \n\
net.ipv4.ip_forward                 = 1 \n\
net.bridge.bridge-nf-call-ip6tables = 1" >> /etc/sysctl.d/99-kubernetes-cri.conf

# Apply sysctl params without reboot
RUN sysctl --system

# Install Containerd
RUN wget https://github.com/containerd/containerd/releases/download/v${CONTAINERD_VERSION}/cri-containerd-cni-${CONTAINERD_VERSION}-linux-${ARCH}.tar.gz
RUN tar --no-overwrite-dir -C / -xzf cri-containerd-cni-${CONTAINERD_VERSION}-linux-amd64.tar.gz && rm -f cri-containerd-cni-${CONTAINERD_VERSION}-linux-amd64.tar.gz

### Add apt repos
# Kubeadm, Kubelet, and Kubectl for EKS-D 1.20
RUN curl -fsSLo /usr/bin/kubelet https://distro.eks.amazonaws.com/kubernetes-1-20/releases/10/artifacts/kubernetes/v1.20.11/bin/linux/amd64/kubelet && chmod +x /usr/bin/kubelet
RUN curl -fsSLo /usr/bin/kubeadm https://distro.eks.amazonaws.com/kubernetes-1-20/releases/10/artifacts/kubernetes/v1.20.11/bin/linux/amd64/kubeadm && chmod +x /usr/bin/kubeadm
RUN curl -fsSLo /usr/bin/kubectl https://distro.eks.amazonaws.com/kubernetes-1-20/releases/10/artifacts/kubernetes/v1.20.11/bin/linux/amd64/kubectl && chmod +x /usr/bin/kubectl

RUN mkdir -p /etc/kubernetes/manifests

RUN mkdir -p /etc/containerd && containerd config default > /etc/containerd/config.toml
RUN systemctl enable containerd

COPY kubelet.service /lib/systemd/system/

RUN mkdir -p /etc/systemd/system/kubelet.service.d/
COPY 10-kubeadm.conf /etc/systemd/system/kubelet.service.d/

RUN systemctl enable kubelet

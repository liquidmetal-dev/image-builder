ARG BASE_IMAGE
FROM ${BASE_IMAGE}

RUN echo '[Service] \n\
Environment="KUBELET_EXTRA_ARGS=--container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock"' \
    >> /etc/systemd/system/kubelet.service.d/0-containerd.conf

## Remove any kernel bits from the image
RUN rm -rf /lib/modules /boot/*
RUN apt autoclean && apt autoremove && apt clean

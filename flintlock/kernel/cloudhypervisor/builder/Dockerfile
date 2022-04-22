FROM ubuntu:20.04

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
        bc                  \
        bison               \
        build-essential     \
        gcc-7               \
        flex                \
        git                 \
        libelf-dev          \
        libncurses-dev      \
        libssl-dev          \
        ca-certificates     \
        kmod                \
        wget             && \
    update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 10
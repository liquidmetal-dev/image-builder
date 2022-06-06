FROM ghcr.io/weaveworks-liquidmetal/flintlock-ubuntu-base:20.04

ARG IPERF3_VERSION=3.9-1

# sysbench
RUN curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.deb.sh | bash && \
        apt -y install sysbench

# iperf3
RUN apt remove iperf3 libiperf0 && \
        apt install libsctp1 && \
        wget https://iperf.fr/download/ubuntu/libiperf0_${IPERF3_VERSION}_amd64.deb && \
        wget https://iperf.fr/download/ubuntu/iperf3_${IPERF3_VERSION}_amd64.deb && \
        dpkg -i libiperf0_${IPERF3_VERSION}_amd64.deb iperf3_${IPERF3_VERSION}_amd64.deb && \
        rm libiperf0_${IPERF3_VERSION}_amd64.deb iperf3_${IPERF3_VERSION}_amd64.deb

# Apache bench, stress
RUN apt -y install apache2-utils stress-ng
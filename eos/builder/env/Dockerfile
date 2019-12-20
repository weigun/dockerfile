FROM ubuntu:18.04

ENV TOOLS=/root/deps
RUN mkdir -p ${TOOLS}
WORKDIR ${TOOLS}

# install dependencies.
RUN apt-get update && \
    apt-get upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y git make \
    bzip2 automake libbz2-dev libssl-dev doxygen graphviz libgmp3-dev \
    autotools-dev libicu-dev python2.7 python2.7-dev python3 python3-dev \
    autoconf libtool g++ gcc curl zlib1g-dev sudo ruby libusb-1.0-0-dev \
    libcurl4-gnutls-dev pkg-config patch ccache vim-common jq

# build cmake.
RUN curl -LO https://cmake.org/files/v3.13/cmake-3.13.2-Linux-x86_64.tar.gz && \
    tar -xzf cmake-3.13.2-Linux-x86_64.tar.gz && \ 
    ln -sf ${TOOLS}/cmake-3.13.2-Linux-x86_64/bin/* /usr/bin/ && \
    export PATH=${TOOLS}:$PATH && \
    rm -f cmake-3.13.2-Linux-x86_64.tar.gz

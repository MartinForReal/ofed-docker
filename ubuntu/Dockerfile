## Stage: Build driver
ARG D_BASE_IMAGE=ubuntu:20.04
FROM $D_BASE_IMAGE AS build

# Build Args - pass with --build-arg flag during build
ARG D_OFED_VERSION="5.1-2.5.8.0"
ARG D_OS="ubuntu20.04"
ARG D_ARCH="x86_64"
ARG D_OFED_PATH="MLNX_OFED_LINUX-${D_OFED_VERSION}-${D_OS}-${D_ARCH}"

ARG D_OFED_TARBALL_NAME="MLNX_OFED_LINUX-${D_OFED_VERSION}-${D_OS}-${D_ARCH}.tgz"
ARG D_OFED_URL_PATH="https://www.mellanox.com/downloads/ofed/MLNX_OFED-${D_OFED_VERSION}/${D_OFED_TARBALL_NAME}"
WORKDIR /root

# Internal arguments
ARG D_WITHOUT_FLAGS="--without-iser-dkms --without-isert-dkms --without-srp-dkms"
RUN  apt-get update && apt-get install -y --no-install-recommends \
    apt-utils \
    ca-certificates && \
    echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse" > /etc/apt/sources.list && \
    echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse" >> /etc/apt/sources.list && \
    apt-get -yq update && apt-get -yq install perl ifupdown \
    curl -O -L ${D_OFED_URL_PATH}  && tar -xf ${D_OFED_TARBALL_NAME} && \
    /bin/bash -c '/root/MLNX_OFED_LINUX-${D_OFED_VERSION}-${D_OS}-${D_ARCH}/mlnxofedinstall --without-fw-update --force ${D_WITHOUT_FLAGS}' && \
    sed -i '/ESP_OFFLOAD_LOAD=yes/c\ESP_OFFLOAD_LOAD=no' /etc/infiniband/openib.conf && \
    cp /root/MLNX_OFED_LINUX-${D_OFED_VERSION}-${D_OS}-${D_ARCH}/docs/scripts/openibd-post-start-configure-interfaces/post-start-hook.sh /etc/infiniband/post-start-hook.sh && \
    chmod +x /etc/infiniband/post-start-hook.sh && \
    rm -rf /root/MLNX_OFED_LINUX-*

ADD ./entrypoint.sh /root/entrypoint.sh

ENTRYPOINT ["/root/entrypoint.sh"]

FROM ubuntu:22.04

LABEL maintainer "Franck Descollonges <franck_descollonges@fr.ibm.com>"

RUN apt-get update && apt-get install -y --no-install-recommends \
    gnupg2 curl ca-certificates && \
    curl -fsSLO https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/${NVARCH}/cuda-keyring_1.1-1_all.deb && \
    dpkg -i cuda-keyring_1.1-1_all.deb && \
    apt-get update && \
    apt-get -y install cuda-toolkit-12-4 && \
    apt install nvidia-utils-535

# Required for nvidia-docker v1
RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf \
    && echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

# nvidia-container-runtime
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

RUN apt-get update && \
  apt-get install -y python3-pip python3-dev && \
  cd /usr/local/bin && \
  ln -s /usr/bin/python3 python && \
  pip3 --no-cache-dir install --upgrade pip && \
  rm -rf /var/lib/apt/lists/*

WORKDIR /instructlab
RUN chgrp -R 0 /instructlab && \
    chmod -R g=u /instructlab
	
RUN chgrp -R 0 /home && \
    chmod -R g=u /home

CMD ["/bin/bash"]
LABEL com.redhat.component="instructlab"

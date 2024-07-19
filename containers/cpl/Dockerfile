FROM ubuntu:22.04

LABEL maintainer "Franck Descollonges <franck_descollonges@fr.ibm.com>"

ENV NVARCH x86_64
ENV DEBIAN_FRONTEND=noninteractive
ENV NVIDIA_REQUIRE_CUDA "cuda>=12.4 brand=tesla,driver>=470,driver<471 brand=unknown,driver>=470,driver<471 brand=nvidia,driver>=470,driver<471 brand=nvidiartx,driver>=470,driver<471 brand=geforce,driver>=470,driver<471 brand=geforcertx,driver>=470,driver<471 brand=quadro,driver>=470,driver<471 brand=quadrortx,driver>=470,driver<471 brand=titan,driver>=470,driver<471 brand=titanrtx,driver>=470,driver<471 brand=tesla,driver>=525,driver<526 brand=unknown,driver>=525,driver<526 brand=nvidia,driver>=525,driver<526 brand=nvidiartx,driver>=525,driver<526 brand=geforce,driver>=525,driver<526 brand=geforcertx,driver>=525,driver<526 brand=quadro,driver>=525,driver<526 brand=quadrortx,driver>=525,driver<526 brand=titan,driver>=525,driver<526 brand=titanrtx,driver>=525,driver<526 brand=tesla,driver>=535,driver<536 brand=unknown,driver>=535,driver<536 brand=nvidia,driver>=535,driver<536 brand=nvidiartx,driver>=535,driver<536 brand=geforce,driver>=535,driver<536 brand=geforcertx,driver>=535,driver<536 brand=quadro,driver>=535,driver<536 brand=quadrortx,driver>=535,driver<536 brand=titan,driver>=535,driver<536 brand=titanrtx,driver>=535,driver<536"
ENV NV_CUDA_CUDART_VERSION 12.4.127-1
ENV NV_CUDA_COMPAT_PACKAGE cuda-compat-12-4
ENV CUDA_VERSION 12.4.1

# RUN apt-get update && \
#     apt-get install -y software-properties-common && \
#     add-apt-repository ppa:graphics-drivers && \
#     apt install -y nvidia-driver-550 && \
#     apt-get install -y --no-install-recommends gnupg2 curl ca-certificates && \
#     curl -fsSLO https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb && \
#     dpkg -i cuda-keyring_1.1-1_all.deb && \

RUN apt-get update && apt-get install -y --no-install-recommends \
    gnupg2 curl ca-certificates && \
    curl -fsSLO https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/${NVARCH}/cuda-keyring_1.1-1_all.deb && \
    dpkg -i cuda-keyring_1.1-1_all.deb && \
    apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y --no-install-recommends \
    cuda-cudart-12-4=${NV_CUDA_CUDART_VERSION} \
    ${NV_CUDA_COMPAT_PACKAGE} \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && \
    apt-get -y install nvtop && \
    apt-get -y install pciutils

# Required for nvidia-docker v1
RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf \ 
    && echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf

ENV PATH /usr/local/nvidia/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64

# nvidia-container-runtime
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

RUN apt-get update && \
  apt-get install -y python3-pip python3.11 && \
  cd /usr/local/bin && \
  ln -s /usr/bin/python3 python && \
  pip3 --no-cache-dir install --upgrade pip && \
  rm -rf /var/lib/apt/lists/*

RUN useradd -ms /bin/bash -p "$(openssl passwd -1 ilab-pwd) ilab-user
USER ilab-user
WORKDIR /home/ilab-user

ARG USERNAME=ilab-user

RUN export HOME=/home/ilab-user

RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir commandhistory \
    && touch commandhistory/.bash_history \
    && chown -R $USERNAME commandhistory \
    && echo "$SNIPPET" >> "/home/$USERNAME/.bashrc"



CMD ["/bin/bash"]
LABEL com.redhat.component="instructlab"

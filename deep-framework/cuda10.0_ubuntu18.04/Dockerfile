FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu18.04
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

# maximum supported tensorflow is 1.12.0 with CUDA9.0
ARG TENSORFLOW_VERSION=latest
ARG PYTORCH_VERSION=latest
ARG MXNET_VERSION=latest
ARG OPENCV_VERSION=3.4.1
ARG DEBIAN_FRONTEND=noninteractive 

ENV JUPYTER_CONFIG_DIR=/opt/jupyter
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/extras/CUPTI/lib64

# RUN echo -e "\n**********************\nNVIDIA Driver Version\n**********************\n" && \
# 	cat /proc/driver/nvidia/version && \
# 	echo -e "\n**********************\nCUDA Version\n**********************\n" && \
# 	nvcc -V && \
# 	echo -e "\n\nBuilding your Deep Learning Docker Image...\n"

# Install some dependencies
RUN APT_INSTALL="apt-get install -y --no-install-recommends" && \
    PIP_INSTALL="pip install --no-cache-dir" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update --fix-missing && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    $APT_INSTALL cmake wget bzip2 ca-certificates git vim curl unzip  build-essential libglib2.0-0 libxext6 libsm6 libxrender1 mercurial subversion openssh-server openssh-client tree python3.6 python3-pip curl grep sed dpkg && \
# ==================================================================
# opencv3
# ------------------------------------------------------------------
    $PIP_INSTALL opencv-python==${OPENCV_VERSION} && \
# ==================================================================
# tensorflow
# ------------------------------------------------------------------
    $PIP_INSTALL tensorflow-gpu && \
# ==================================================================
# pytorch
# ------------------------------------------------------------------
    $PIP_INSTALL pytorch torchvision && \
# ==================================================================
# mxnet
# ------------------------------------------------------------------
    $PIP_INSTALL mxnet-cu90 mxboard graphviz gluoncv && \
# ==================================================================
# misc
# ------------------------------------------------------------------
    $PIP_INSTALL h5py pandas pyyaml yacs cython scipy scikit-learn scikit-image easydict matplotlib jupyter pip cupy && \
# ==================================================================
# extra bug for tensorboardX https://github.com/tensorflow/models/issues/3995
# ------------------------------------------------------------------
    $PIP_INSTALL --upgrade tensorboardX protobuf==3.6.0  && \
# ==================================================================
# config & cleanup
# ------------------------------------------------------------------
    ldconfig && \
	apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/* && \
# ==================================================================
# jupyter notebook
# ------------------------------------------------------------------
    mkdir /opt/jupyter
COPY jupyter_notebook_config.py /opt/jupyter
# ==================================================================
# ssh server
# ------------------------------------------------------------------
COPY sshd_config /etc/ssh/
COPY run.sh /opt

# ENTRYPOINT [ "/bin/bash", "run.sh", " && "]
# Expose Ports for ssh (22), TensorBoard (6008), Ipython (8890)
EXPOSE 22 6008 8890

WORKDIR /root
CMD ["/bin/bash"]
FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

# maximum supported tensorflow is 1.12.0 with CUDA9.0
ARG TENSORFLOW_VERSION=1.12.0
ARG PYTORCH_VERSION=latest
ARG MXNET_VERSION=latest
ARG OPENCV_VERSION=latest
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
    PIP_INSTALL="python -m pip --no-cache-dir install --upgrade" && \
    GIT_CLONE="git clone --depth 10" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update --fix-missing && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL cmake wget bzip2 ca-certificates git vim curl unzip build-essential libglib2.0-0 libxext6 libsm6 libxrender1 mercurial subversion openssh-server openssh-client tree curl grep sed dpkg && \
# ==================================================================
# python3.6
# ------------------------------------------------------------------
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL software-properties-common && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL python3.6 python3.6-dev python3-distutils-extra && \
    wget -O ~/get-pip.py https://bootstrap.pypa.io/get-pip.py && \
    python3.6 ~/get-pip.py && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python && \
    $PIP_INSTALL setuptools h5py pandas pyyaml yacs cython scipy scikit-learn scikit-image easydict matplotlib jupyter cupy numpy cloudpickle tensorboardX && \
# ==================================================================
# opencv3
# ------------------------------------------------------------------
    $PIP_INSTALL opencv-python && \
# ==================================================================
# tensorflow
# ------------------------------------------------------------------
    $PIP_INSTALL tensorflow-gpu==${TENSORFLOW_VERSION} && \
# ==================================================================
# pytorch
# ------------------------------------------------------------------
    $PIP_INSTALL torch torchvision && \
# ==================================================================
# mxnet
# ------------------------------------------------------------------
    $PIP_INSTALL mxnet-cu90 mxboard graphviz gluoncv && \
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
# Expose Ports for ssh (22), TensorBoard (6006), Ipython (8888)
EXPOSE 22 6006 8888

WORKDIR /root
CMD ["/bin/bash"]
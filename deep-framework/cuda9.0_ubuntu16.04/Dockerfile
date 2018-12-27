FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

ARG TENSORFLOW_VERSION=latest
ARG PYTORCH_VERSION=latest
ARG MXNET_VERSION=latest
ARG DEBIAN_FRONTEND=noninteractive
ARG APT_INSTALL="apt-get install -y --no-install-recommends"
ARG PIP_INSTALL="python -m pip --no-cache-dir install --upgrade"

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /usr/local/bin:$PATH

#RUN echo -e "\n**********************\nNVIDIA Driver Version\n**********************\n" && \
#	cat /proc/driver/nvidia/version && \
#	echo -e "\n**********************\nCUDA Version\n**********************\n" && \
#	nvcc -V && \
#	echo -e "\n\nBuilding your Deep Learning Docker Image...\n"

# Install some dependencies
RUN rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update --fix-missing && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    $APT_INSTALL cmake make wget bzip2 ca-certificates git vim curl unzip build-essential libglib2.0-0 libxext6 libsm6 libxrender1 grep dpkg zlib1g-dev tk-dev python3-certifi libffi-dev libssl-dev zlib1g-dev openssh-server openssh-client && \
# ==================================================================
# python3.6 and pip18.1
# ------------------------------------------------------------------
    $APT_INSTALL software-properties-common && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && \
    $APT_INSTALL python3.6 python3.6-dev && \
    wget -O ~/get-pip.py https://bootstrap.pypa.io/get-pip.py && \
    python3.6 ~/get-pip.py && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python && \
    $PIP_INSTALL setuptools && \
    $PIP_INSTALL numpy scipy pandas cloudpickle scikit-learn matplotlib Cython sklearn scikit-image pyyaml easydict argparse Pillow && \
# ==================================================================
# boost
# ------------------------------------------------------------------
	wget -O ~/boost.tar.gz https://dl.bintray.com/boostorg/release/1.65.1/source/boost_1_65_1.tar.gz && \
    tar -zxf ~/boost.tar.gz -C ~ && \
    cd ~/boost_* && \
    ./bootstrap.sh --with-python=python3.6 && \
    ./b2 install --prefix=/usr/local && \
# ==================================================================
# opencv
# ------------------------------------------------------------------
	$PIP_INSTALL opencv-python && \
# ==================================================================
# jupyter
# ------------------------------------------------------------------
	$PIP_INSTALL jupyter && \
# ==================================================================
# mxnet
# ------------------------------------------------------------------
	$APT_INSTALL libatlas-base-dev graphviz && \
    $PIP_INSTALL mxnet-cu90 graphviz mxboard gluoncv && \
# ==================================================================
# pytorch
# ------------------------------------------------------------------
	$PIP_INSTALL torch torchvision tensorboard && \
# ==================================================================
# tensorflow
# ------------------------------------------------------------------
	$PIP_INSTALL tensorflow-gpu && \
# ==================================================================
# extra bug for tensorboardX https://github.com/tensorflow/models/issues/3995
# ------------------------------------------------------------------
    $PIP_INSTALL tensorboardX protobuf==3.6.0 pip easydict Cython && \
# ==================================================================
# config & cleanup
# ------------------------------------------------------------------
	ldconfig && \
	apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*

# Set up notebook config
COPY jupyter_notebook_config.py /root/.jupyter/

# Set up ssh server
COPY sshd_config /etc/ssh/

# Jupyter has issues with being run directly: https://github.com/ipython/ipython/issues/7062
COPY run.sh /root/

# ENTRYPOINT [ "/bin/bash", "run.sh", " && "]
# Expose Ports for ssh (22), TensorBoard (6008), Ipython (8890)
EXPOSE 22 6008 8890

WORKDIR /root
CMD ["/bin/bash"]
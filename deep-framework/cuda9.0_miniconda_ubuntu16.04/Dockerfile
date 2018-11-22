FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

ARG TENSORFLOW_VERSION=latest
ARG PYTORCH_VERSION=latest
ARG MXNET_VERSION=latest
ARG DEBIAN_FRONTEND=noninteractive 

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/miniconda/bin:$PATH

#RUN echo -e "\n**********************\nNVIDIA Driver Version\n**********************\n" && \
#	cat /proc/driver/nvidia/version && \
#	echo -e "\n**********************\nCUDA Version\n**********************\n" && \
#	nvcc -V && \
#	echo -e "\n\nBuilding your Deep Learning Docker Image...\n"

# Install some dependencies
RUN APT_INSTALL="apt-get install -y --no-install-recommends" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update --fix-missing && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    $APT_INSTALL cmake wget bzip2 ca-certificates git vim curl unzip  build-essential libglib2.0-0 libxext6 libsm6 libxrender1 mercurial subversion && \
    apt-get install -y curl grep sed dpkg && \
    apt-get clean && \
# ==================================================================
# miniconda3
# ------------------------------------------------------------------
    wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.4-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
    rm ~/miniconda.sh && \
    ln -s /opt/miniconda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/miniconda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
# ==================================================================
# opencv3
# ------------------------------------------------------------------
    conda install -y opencv -c anaconda && \
# ==================================================================
# jupyter
# ------------------------------------------------------------------
    $PIP_INSTALL jupyter && \
# ==================================================================
# matplotlib, misc
# ------------------------------------------------------------------
    $PIP_INSTALL sklearn scikit-image matplotlib Cython yaml json easydict && \
# ==================================================================
# tensorflow
# ------------------------------------------------------------------
    conda install -y tensorflow-gpu -c anaconda && \
# ==================================================================
# pytorch
# ------------------------------------------------------------------
    conda install -y pytorch torchvision cuda90 -c pytorch && \
# ==================================================================
# mxnet
# ------------------------------------------------------------------
    conda install -y graphviz && \
    pip install mxnet-cu90 mxboard graphviz && \
# ==================================================================
# extra
# ------------------------------------------------------------------
    pip install tensorboardX easydict && \
# ==================================================================
# config & cleanup
# ------------------------------------------------------------------
    ldconfig && \
	apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*

# Set up notebook config
COPY jupyter_notebook_config.py /root/.jupyter/

# Jupyter has issues with being run directly: https://github.com/ipython/ipython/issues/7062
COPY run_jupyter.sh /root/

# Expose Ports for TensorBoard (6008), Ipython (8890)
EXPOSE 6008 8890

WORKDIR "/root"
CMD ["/bin/bash"]

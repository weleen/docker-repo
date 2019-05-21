FROM nvidia/cuda:8.0-cudnn7-devel-ubuntu16.04
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

ARG TENSORFLOW_VERSION=1.4.0
ARG PYTORCH_VERSION=latest
ARG MXNET_VERSION=latest
ARG DEBIAN_FRONTEND=noninteractive 

ENV JUPYTER_CONFIG_DIR=/opt/jupyter
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/anaconda3/bin:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/extras/CUPTI/lib64

# RUN echo -e "\n**********************\nNVIDIA Driver Version\n**********************\n" && \
# 	cat /proc/driver/nvidia/version && \
# 	echo -e "\n**********************\nCUDA Version\n**********************\n" && \
# 	nvcc -V && \
# 	echo -e "\n\nBuilding your Deep Learning Docker Image...\n"

# Install some dependencies
RUN APT_INSTALL="apt-get install -y --no-install-recommends" && \
    PIP_INSTALL="pip install --no-cache-dir" && \
    CONDA_INSTALL="conda install -y" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update --fix-missing && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    $APT_INSTALL cmake wget bzip2 ca-certificates git vim curl unzip  build-essential libglib2.0-0 libxext6 libsm6 libxrender1 mercurial subversion openssh-server openssh-client tree && \
# ==================================================================
# anaconda3
# ------------------------------------------------------------------
    wget --quiet https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/anaconda3 && \
    rm ~/anaconda.sh && \
    ln -s /opt/anaconda3/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/anaconda3/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    apt-get install -y curl grep sed dpkg && \
    apt-get clean && \
# ==================================================================
# opencv3
# ------------------------------------------------------------------
    $CONDA_INSTALL opencv -c anaconda && \
# ==================================================================
# tensorflow
# ------------------------------------------------------------------
    $PIP_INSTALL tensorflow-gpu==${TENSORFLOW_VERSION} && \
# ==================================================================
# pytorch
# ------------------------------------------------------------------
    $CONDA_INSTALL pytorch torchvision -c pytorch && \
# ==================================================================
# mxnet
# ------------------------------------------------------------------
    $PIP_INSTALL mxnet-cu80 mxboard graphviz gluoncv && \
# ==================================================================
# extra bug for tensorboardX https://github.com/tensorflow/models/issues/3995
# ------------------------------------------------------------------
    $PIP_INSTALL --upgrade tensorboardX protobuf==3.6.0 pip easydict Cython cupy yacs && \
# ==================================================================
# config & cleanup
# ------------------------------------------------------------------
    ldconfig && \
	apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/* && \
    conda clean --all && \
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

FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04
ENV LC_ALL=C.UTF-8
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

ARG PYTORCH_VERSION=latest
ARG OPENCV_VERSION=latest
ARG DEBIAN_FRONTEND=noninteractive 

ENV JUPYTER_CONFIG_DIR=/opt/jupyter
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/extras/CUPTI/lib64
ENV PATH=/root/miniconda/bin:$PATH
ENV CONDA_AUTO_UPDATE_CONDA=false

# RUN echo -e "\n**********************\nNVIDIA Driver Version\n**********************\n" && \
# 	cat /proc/driver/nvidia/version && \
# 	echo -e "\n**********************\nCUDA Version\n**********************\n" && \
# 	nvcc -V && \
# 	echo -e "\n\nBuilding your Deep Learning Docker Image...\n"

# Install some basic utilities
RUN . /etc/os-release; \
		printf "deb http://ppa.launchpad.net/jonathonf/vim/ubuntu %s main" "$UBUNTU_CODENAME" main | tee /etc/apt/sources.list.d/vim-ppa.list && \
		printf "deb http://ppa.launchpad.net/git-core/ppa/ubuntu %s main" "$UBUNTU_CODENAME" main | tee /etc/apt/sources.list.d/git-ppa.list && \
		apt-key  adv --keyserver hkps://keyserver.ubuntu.com --recv-key 4AB0F789CBA31744CC7DA76A8CF63AD3F06FC659 && \
		apt-key  adv --keyserver hkps://keyserver.ubuntu.com --recv-key E1DD270288B4E6030699E45FA1715D88E1DF1F24 && \
		apt-get update && \
	APT_INSTALL="apt-get install -y --no-install-recommends" && \
    PIP_INSTALL="python -m pip --no-cache-dir install --upgrade" && \
    GIT_CLONE="git clone --depth 10" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update --fix-missing && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL cmake wget bzip2 ca-certificates git vim curl unzip build-essential graphviz sudo libglib2.0-0 libxext6 libsm6 libxrender1 mercurial subversion openssh-server openssh-client tree curl grep sed dpkg && \
# ==================================================================
# python3.6
# ------------------------------------------------------------------
# Install Miniconda
	curl -so ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh \
			&& bash ~/miniconda.sh -b -p ~/miniconda \
			&& rm ~/miniconda.sh && \
# ==================================================================
# opencv3
# ------------------------------------------------------------------
    $PIP_INSTALL opencv-python && \
# ==================================================================
# pytorch
# ------------------------------------------------------------------
    $PIP_INSTALL torch torchvision && \
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
    mkdir /opt/jupyter && \
    conda install -c anaconda jupyter && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager -y --clean && \
	jupyter labextension install @jupyterlab/toc -y --clean && \
	jupyter labextension install jupyter-matplotlib -y --clean && \
	jlpm cache clean
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
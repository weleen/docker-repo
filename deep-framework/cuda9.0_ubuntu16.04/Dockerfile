FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04
LABEL maintainer "yimingwu <yimingwu@hotmail.com>"

ARG TENSORFLOW_VERSION=latest
ARG PYTORCH_VERSION=latest
ARG MXNET_VERSION=latest
ARG DEBIAN_FRONTEND=noninteractive

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /usr/local/bin:$PATH

#RUN echo -e "\n**********************\nNVIDIA Driver Version\n**********************\n" && \
#	cat /proc/driver/nvidia/version && \
#	echo -e "\n**********************\nCUDA Version\n**********************\n" && \
#	nvcc -V && \
#	echo -e "\n\nBuilding your Deep Learning Docker Image...\n"

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
    $APT_INSTALL cmake make wget bzip2 ca-certificates git vim curl unzip build-essential libglib2.0-0 libxext6 libsm6 libxrender1 grep dpkg zlib1g-dev
# ==================================================================
# python3.6.7
# ------------------------------------------------------------------

# ENV GPG_KEY 0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
ENV PYTHON_VERSION 3.6.7
ENV PYTHON_PIP_VERSION 18.1
RUN $APT_INSTALL python3.6 python3.6-dev python3-pip && \
    wget https://bootstrap.pypa.io/get-pip.py && \
    python3.6 --no-cache-dir get-pip.py && \
    ln -s /usr/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /usr/local/bin/pip /usr/local/bin/pip3 && \
    rm -f get-pip.py

# RUN set -ex \
# 	\
# 	&& wget -O python.tar.xz "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz" \
# 	&& wget -O python.tar.xz.asc "https://www.python.org/ftp/python/${PYTHON_VERSION%%[a-z]*}/Python-$PYTHON_VERSION.tar.xz.asc" \
# 	&& export GNUPGHOME="$(mktemp -d)" \
# 	&& gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$GPG_KEY" \
# 	&& gpg --batch --verify python.tar.xz.asc python.tar.xz \
# 	&& { command -v gpgconf > /dev/null && gpgconf --kill all || :; } \
# 	&& rm -rf "$GNUPGHOME" python.tar.xz.asc \
# 	&& mkdir -p /usr/src/python \
# 	&& tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
# 	&& rm python.tar.xz \
# 	\
# 	&& cd /usr/src/python \
# 	&& gnuArch="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" \
# 	&& ./configure \
# 		--build="$gnuArch" \
# 		--enable-loadable-sqlite-extensions \
# 		--enable-shared \
# 		--with-system-expat \
# 		--with-system-ffi \
# 		--without-ensurepip \
# 	&& make -j "$(nproc)" \
# 	&& make install \
# 	&& ldconfig \
# 	\
# 	&& find /usr/local -depth \
# 		\( \
# 			\( -type d -a \( -name test -o -name tests \) \) \
# 			-o \
# 			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
# 		\) -exec rm -rf '{}' + \
# 	&& rm -rf /usr/src/python \
# 	\
# 	&& python3 --version && \
#     cd /usr/local/bin \
# 	&& ln -s idle3 idle \
# 	&& ln -s pydoc3 pydoc \
# 	&& ln -s python3 python \
# 	&& ln -s python3-config python-config

# RUN set -ex; \
# 	\
# 	wget -O get-pip.py 'https://bootstrap.pypa.io/get-pip.py'; \
# 	\
# 	python get-pip.py \
# 		--disable-pip-version-check \
# 		--no-cache-dir \
# 		"pip==$PYTHON_PIP_VERSION" \
# 	; \
# 	pip --version; \
# 	\
# 	find /usr/local -depth \
# 		\( \
# 			\( -type d -a \( -name test -o -name tests \) \) \
# 			-o \
# 			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
# 		\) -exec rm -rf '{}' +; \
# 	rm -f get-pip.py
# ==================================================================
# misc
# ------------------------------------------------------------------
RUN $PIP_INSTALL setuptools numpy scipy pandas cloudpickle scikit-learn matplotlib Cython sklearn scikit-image yaml json easydict argparse Pillow PIL image
# ==================================================================
# boost
# ------------------------------------------------------------------
RUN wget -O ~/boost.tar.gz https://dl.bintray.com/boostorg/release/1.65.1/source/boost_1_65_1.tar.gz && \
    tar -zxf ~/boost.tar.gz -C ~ && \
    cd ~/boost_* && \
    ./bootstrap.sh --with-python=python3.6 && \
    ./b2 install --prefix=/usr/local
# ==================================================================
# opencv
# ------------------------------------------------------------------
RUN $PIP_INSTALL opencv-python
# ==================================================================
# jupyter
# ------------------------------------------------------------------
RUN $PIP_INSTALL jupyter
# ==================================================================
# mxnet
# ------------------------------------------------------------------
RUN $APT_INSTALL libatlas-base-dev graphviz && \
    $PIP_INSTALL mxnet-cu90 graphviz mxboard
# ==================================================================
# pytorch
# ------------------------------------------------------------------
RUN $PIP_INSTALL torch torchvision tensorboardX
# ==================================================================
# tensorflow
# ------------------------------------------------------------------
RUN $PIP_INSTALL tensorflow-gpu
# ==================================================================
# config & cleanup
# ------------------------------------------------------------------
RUN ldconfig && \
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

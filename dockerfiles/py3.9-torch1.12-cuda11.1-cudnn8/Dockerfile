FROM ubuntu:focal
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak && sed -i 's/http:\/\/archive.ubuntu.com/http:\/\/mirrors.aliyun.com/g' /etc/apt/sources.list && sed -i 's/http:\/\/security.ubuntu.com/http:\/\/mirrors.aliyun.com/g' /etc/apt/sources.list && apt clean
RUN apt update && apt install tzdata -y
ENV TZ="Asia/Shanghai"
RUN apt update && apt install -y --no-install-recommends gnupg2 curl ca-certificates && curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub | apt-key add - && curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/7fa2af80.pub | apt-key add - && echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /" > /etc/apt/sources.list.d/cuda.list && apt purge --autoremove -y curl && rm -rf /var/lib/apt/lists/*
ENV CUDA_VERSION=11.1.0
RUN apt update && apt install -y --no-install-recommends cuda-cudart-11-1=11.1.74-1 cuda-compat-11-1 && ln -s cuda-11.1 /usr/local/cuda && rm -rf /var/lib/apt/lists/*
RUN echo "/usr/local/nvidia/lib" >> /etc/ld.so.conf.d/nvidia.conf && echo "/usr/local/nvidia/lib64" >> /etc/ld.so.conf.d/nvidia.conf && echo 'deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64 /' >> /etc/apt/sources.list.d/cuda.list
ENV PATH=/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
ENV LD_LIBRARY_PATH=/usr/local/nvidia/lib:/usr/local/nvidia/lib64
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility
ENV NVIDIA_REQUIRE_CUDA=cuda>=11.1 brand=tesla,driver>=450,driver<451
LABEL maintainer=yunchao<yunchaozk@gmail.com>
ENV NCCL_VERSION=2.8.3
RUN apt update && apt install -y --no-install-recommends cuda-libraries-11-1=11.1.1-1 libnpp-11-1=11.1.2.301-1 cuda-nvtx-11-1=11.1.74-1 libcublas-11-1=11.3.0.106-1 libnccl2=2.8.4-1+cuda11.1 && apt-mark hold libnccl2 && rm -rf /var/lib/apt/lists/*
RUN apt update && apt install -y --no-install-recommends cuda-nvml-dev-11-1=11.1.74-1 cuda-command-line-tools-11-1=11.1.1-1 cuda-nvprof-11-1=11.1.105-1 libnpp-dev-11-1=11.1.2.301-1 cuda-libraries-dev-11-1=11.1.1-1 cuda-minimal-build-11-1=11.1.1-1 libnccl-dev=2.8.4-1+cuda11.1 libcublas-dev-11-1=11.3.0.106-1 libcusparse-11-1=11.3.0.10-1 libcusparse-dev-11-1=11.3.0.10-1 && apt-mark hold libnccl-dev && rm -rf /var/lib/apt/lists/*
RUN apt update && apt install libcudnn8=8.0.5.39-1+cuda11.1 libcudnn8-dev=8.0.5.39-1+cuda11.1
ENV LIBRARY_PATH=/usr/local/cuda/lib64/stubs
ARG python=3.9
ENV PYTHON_VERSION=3.9
SHELL ["/bin/bash", "-cu"]
RUN ln -s /usr/include/cudnn* /usr/local/cuda/include && ln -s /usr/lib/x86_64-linux-gnu/libcudnn* /usr/local/cuda/lib64
RUN apt install -y apt-transport-https
RUN apt -o Acquire::Check-Valid-Until=false-o Acquire::Check-Date=false update && apt install -y --allow-downgrades --allow-change-held-packages --no-install-recommends build-essential cmake software-properties-common git curl vim wget net-tools inetutils-ping bzip2 unzip ca-certificates libjpeg-dev libpng-dev python${PYTHON_VERSION} python${PYTHON_VERSION}-dev librdmacm1 libibverbs1 ibverbs-providers &&rm -rf /var/lib/apt/lists/*
RUN add-apt-repository universe
RUN if [[ "${PYTHON_VERSION}" == "3.9" ]]; then apt-o Acquire::Check-Valid-Until=false -o Acquire::Check-Date=false update && apt install -y python${PYTHON_VERSION}-distutils; fi
RUN ln -s /usr/bin/python${PYTHON_VERSION} /usr/bin/python
RUN curl -O https://bootstrap.pypa.io/get-pip.py && python get-pip.py && rm get-pip.py
RUN pip --no-cache-dir install futuretypingnumpy tensorboard h5py && rm -rf /root/.cache/pip/http/*
RUN pip --no-cache-dir install torch==1.12.1+cu113 torchvision==0.13.1+cu113 torchaudio==0.12.1 -f https://download.pytorch.org/whl/torch_stable.html &&rm -rf /root/.cache/pip/http/*
RUN mkdir /tmp/openmpi && cd /tmp/openmpi && wget https://www.open-mpi.org/software/ompi/v4.1/downloads/openmpi-4.1.5.tar.gz && tar zxf openmpi-4.1.5.tar.gz && cd openmpi-4.1.5 && ./configure --enable-orterun-prefix-by-default--with-cuda=/usr/local/cuda&& make -j $(nproc) all && make install && ldconfig && rm -rf /tmp/openmpi
RUN ldconfig /usr/local/cuda/targets/x86_64-linux/lib/stubs && HOROVOD_GPU_ALLREDUCE=NCCLHOROVOD_WITH_PYTORCH=1 pip install --no-cache-dir horovod==0.27.0 && ldconfig
RUN apt install -y --no-install-recommends openssh-client openssh-server && mkdir -p /var/run/sshd
RUN cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new && echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new && cat /etc/ssh/sshd_config | grep -vPermitRootLogin> /etc/ssh/sshd_config.new && echo "PermitRootLogin yes" >> /etc/ssh/sshd_config.new && mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config && mv /etc/ssh/sshd_config.new /etc/ssh/sshd_config
RUN apt install -y --no-install-recommends subversion && svn checkout https://github.com/horovod/horovod/trunk/examples && rm -rf /examples/.svn
RUN pip --no-cache-dir install jupyter jupyterlab pandas scipy opencv-python tensorboardX mxboardtqdm mpi4py matplotlib openpyxl visdom scikit-learn keras && rm -rf /root/.cache/pip/http/*
RUN apt install -y --allow-downgrades --allow-change-held-packages --no-install-recommends libsm-dev libxrender1 libxext-dev&&apt clean &&rm -rf /var/lib/apt/lists/*
RUN mkdir /etc/jupyter/ && wget -P /etc/jupyter/https://cdn.jsdelivr.net/gh/YunchaoZheng/inspur_Aistation_Dockerfile/inspur_jupyter_config/files/jupyter_notebook_config.py
RUN wget -P /etc/jupyter/ https://cdn.jsdelivr.net/gh/YunchaoZheng/inspur_Aistation_Dockerfile/inspur_jupyter_config/files/custom.js
RUN apt clean && rm -rf /var/lib/apt/lists/* && rm -rf /root/.cache/pip/http/*
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/nvidia/lib:/usr/local/nvidia/lib64
WORKDIR /examples

FROM ubuntu:jammy
RUN apt-get update && apt-get install tzdata -y
ENV TZ="Asia/Shanghai"
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
LABEL maintainer=yunchao<yunchaozk@gmail.com>
SHELL ["/bin/bash", "-cu"]
ENV PYTHON_VERSION=3.11
RUN apt-get install -y apt-transport-https
RUN apt-get install -y --allow-downgrades --allow-change-held-packages --no-install-recommends \
    build-essential cmake software-properties-common git curl vim wget net-tools \
    inetutils-ping bzip2 unzip ca-certificates libjpeg-dev libpng-dev python${PYTHON_VERSION} python${PYTHON_VERSION}-dev librdmacm1 libibverbs1 ibverbs-providers &&rm -rf /var/lib/apt/lists/*
RUN add-apt-repository universe
RUN if [[ "${PYTHON_VERSION}" == "3.11" ]]; then apt-get install -y python${PYTHON_VERSION}-distutils; fi
RUN ln -s /usr/bin/python${PYTHON_VERSION} /usr/bin/python
RUN curl -O https://bootstrap.pypa.io/get-pip.py && python get-pip.py && rm get-pip.py
RUN apt-get install -y --no-install-recommends openssh-client openssh-server && mkdir -p /var/run/sshd
RUN cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new && echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new && cat /etc/ssh/sshd_config | grep -vPermitRootLogin> /etc/ssh/sshd_config.new && echo "PermitRootLogin yes" >> /etc/ssh/sshd_config.new && mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config && mv /etc/ssh/sshd_config.new /etc/ssh/sshd_config
RUN pip --no-cache-dir install jupyter jupyterlab scipy matplotlib tqdm scikit-learn && rm -rf /root/.cache/pip/http/*
RUN apt-get update && apt-get install -y --no-install-recommends r-base r-base-dev && apt-get clean && rm -rf /var/lib/apt/lists/*
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    fonts-liberation \
    # - pandoc is used to convert notebooks to html files
    #   it's not present in the aarch64 Ubuntu image, so we install it here
    pandoc \
    # - run-one - a wrapper script that runs no more
    #   than one unique  instance  of  some  command with a unique set of arguments,
    #   we use `run-one-constantly` to support the `RESTARTABLE` option
    run-one && \
    apt-get clean && rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get install -yq --no-install-recommends \
    build-essential \
    emacs-nox \
    vim-tiny \
    git \
    inkscape \
    jed \
    libsm6 \
    libxext-dev \
    libxrender1 \
    lmodern \
    netcat \
    # ---- nbconvert dependencies ----
    texlive-xetex \
    texlive-fonts-recommended \
    texlive-plain-generic \
    # ----
    tzdata \
    unzip \
    nano \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    fonts-dejavu \
    unixodbc \
    unixodbc-dev \
    r-cran-rodbc \
    gfortran \
    gcc && \
    rm -rf /var/lib/apt/lists/*
RUN ln -s /bin/tar /bin/gtar
ENV R_PACKAGES="c('IRkernel', 'dplyr', 'ggplot2', 'DataExplorer', 'caret', 'Boruta', 'pROC', 'MLeval', 'catboost', 'doParallel', 'data.table')"
RUN R -e "install.packages(${R_PACKAGES}, repos='http://cran.us.r-project.org/')"
RUN R -e "IRkernel::installspec(user = FALSE)"
RUN mkdir /etc/jupyter/ && wget -P /etc/jupyter/ https://cdn.jsdelivr.net/gh/YunchaoZheng/inspur_Aistation_Dockerfile/inspur_jupyter_config/files/jupyter_notebook_config.py
RUN wget -P /etc/jupyter/ https://cdn.jsdelivr.net/gh/YunchaoZheng/inspur_Aistation_Dockerfile/inspur_jupyter_config/files/custom.js
RUN apt clean && rm -rf /var/lib/apt/lists/* && rm -rf /root/.cache/pip/http/*
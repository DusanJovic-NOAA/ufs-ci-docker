FROM centos:7

LABEL maintainer="dusan.jovic@noaa.gov"

RUN yum -y update
RUN yum clean all
RUN yum -y install centos-release-scl
RUN yum -y install devtoolset-8-gcc-c++ devtoolset-8-gcc-gfortran
RUN yum -y install \
           autoconf \
           automake \
           curl \
           git \
           libtool \
           libxml2-devel \
           make \
           python27 \
           which
RUN yum clean all

RUN echo export PATH=/opt/rh/devtoolset-8/root/usr/bin/:\$PATH >> /etc/bashrc

RUN cd /tmp && \
    curl -f -s -S -R -L -O https://github.com/Kitware/CMake/releases/download/v3.17.0/cmake-3.17.0-Linux-x86_64.tar.gz && \
    mkdir -p /usr/local/cmake && \
    tar zxvf cmake-3.17.0-Linux-x86_64.tar.gz -C /usr/local/cmake --strip-components=1 && \
    rm cmake-3.17.0-Linux-x86_64.tar.gz && \
    echo export PATH=/usr/local/cmake/bin:\$PATH >> /etc/bashrc

RUN useradd -ms /bin/bash builder
ENV HOME /home/builder
WORKDIR $HOME
RUN mkdir $HOME/ufs-ci-docker
COPY . $HOME/ufs-ci-docker
RUN chown -R builder:builder $HOME/ufs-ci-docker

USER builder

RUN cd $HOME && \
    curl -f -s -S -R -L -O https://ftp.emc.ncep.noaa.gov/EIB/UFS/simple-test-case.tar.gz && \
    tar zxvf simple-test-case.tar.gz && \
    rm simple-test-case.tar.gz

RUN . /etc/bashrc && \
    cd $HOME/ufs-ci-docker/libs/mpilibs && \
    ./build.sh gnu && \
    rm -rf src && \
    echo "export PATH=$HOME/ufs-ci-docker/libs/mpilibs/local/mpich3/bin:\$PATH" >> $HOME/.bashrc

RUN . /etc/bashrc && \
    . $HOME/.bashrc && \
    cd $HOME/ufs-ci-docker && \
    ./get.sh && \
    ./build.sh gnu -3rdparty -nceplibs && \
    rm -rf libs/3rdparty/src libs/nceplibs/EMC_crtm libs/nceplibs/NCEPLIBS-*

CMD ["/bin/bash"]

FROM ubuntu:22.04

# ROOT deps
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y -q install \
    git curl sudo wget  #ipython3.10 python3-pip 

ENV CONDA_DIR /opt/conda
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda
# Put conda in path so we can use conda activate
ENV PATH=$CONDA_DIR/bin:$PATH

RUN conda init bash
RUN echo "conda activate base" >> ~/.bashrc

SHELL ["conda", "run", "-n", "base", "/bin/bash", "-c"]

RUN conda install --solver=classic conda-forge::conda-libmamba-solver conda-forge::libmamba conda-forge::libmambapy conda-forge::libarchive
RUN conda install -c conda-forge -y python=3.11 root==6.32 boost-cpp htcondor bokeh==3.1.0

ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

RUN python3 -m pip install -U pip setuptools wheel

#RUN curl -fsSL https://research.cs.wisc.edu/htcondor/repo/keys/HTCondor-Release.gpg.key | apt-key add - \
#    && echo  "deb [arch=amd64] https://research.cs.wisc.edu/htcondor/repo/ubuntu/8.9 focal main\ndeb-src https://research.cs.wisc.edu/htcondor/repo/ubuntu/8.9 focal main" > /etc/apt/sources.list.d/htcondor.list

RUN apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich #apt-get install -y htcondor

RUN python3 -m pip install \
    htcondor \
    click \
    ipython \
    "dask[complete]==2024.4.1" \
    dask-jobqueue \
    dogpile.cache

WORKDIR /usr/local/share/comp-dev-cms-ita

# Install dask remote jobqueue
RUN git clone --branch AF20-experimental https://github.com/ttedeschi/dask-remote-jobqueue
# WORKDIR /usr/local/share/comp-dev-cms-ita/dask-remote-jobqueue
# RUN python3 setup.py install
# WORKDIR /usr/local/share/comp-dev-cms-ita
RUN cd /usr/local/share/comp-dev-cms-ita/dask-remote-jobqueue \
    && python3 -m pip install -U .

COPY ./condor_config /etc/condor/condor_config
COPY ./ca.crt /ca.crt

#RUN apt-key adv --keyserver hkp://pgp.surfnet.nl --recv-keys ACDFB08FDC962044D87FF00B512839863D487A87

#RUN apt-get -y install software-properties-common && add-apt-repository "deb http://repo.data.kit.edu/ubuntu/bionic ./" && add-apt-repository universe

#RUN apt-get -y install wget 

#RUN wget http://archive.ubuntu.com/ubuntu/pool/universe/q/qrencode/libqrencode3_3.4.4-1build1_amd64.deb && dpkg -i libqrencode3_3.4.4-1build1_amd64.deb

RUN apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y install oidc-agent-cli

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y -q install \
    dpkg-dev cmake g++ gcc binutils libx11-dev libxpm-dev \
    libxft-dev libxext-dev libssl-dev gfortran libpcre3-dev \
    xlibmesa-glu-dev libglew-dev libftgl-dev \
    libmysqlclient-dev libfftw3-dev libcfitsio-dev \
    graphviz-dev libavahi-compat-libdnssd-dev \
    libldap2-dev libxml2-dev libkrb5-dev wget \
    libgsl0-dev git python3 python3-pip curl sudo \
    libblas-dev protobuf-compiler voms-clients

RUN openssl rand -writerand /root/.rnd

ENV X509_CERT_DIR=/cvmfs/cms.cern.ch/grid/etc/grid-security/certificates

RUN python3 -m pip install ipykernel ipywidgets matplotlib

RUN apt-get -y install davix

RUN echo /usr/local/lib/root >> /etc/ld.so.conf && ldconfig
ENV PYTHONPATH /usr/local/lib/root:$PYTHONPATH
ENV CLING_STANDARD_PCH none

RUN python3 -m pip install rucio-clients 

#RUN python3 -m pip install correctionlib
RUN conda install -c conda-forge -y correctionlib

ENV correctionlib_DIR /opt/conda/lib/python3.11/site-packages/correctionlib/cmake/
ENV CPATH /opt/conda/lib/python3.11/site-packages/correctionlib/include

RUN git clone https://gitlab.cern.ch/cp3-cms/CMSJMECalculators.git
RUN sed -i 's/find_package(Python)/find_package(Python 3.11 REQUIRED COMPONENTS Interpreter Development)/g' CMSJMECalculators/CMakeLists.txt
RUN cmake -DPython3_EXECUTABLE=/opt/conda/bin/python3 -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/conda CMSJMECalculators
RUN make
RUN make install

RUN wget https://go.dev/dl/go1.23.3.linux-amd64.tar.gz
RUN rm -rf /usr/local/go && tar -C /usr/local -xzf go1.23.3.linux-amd64.tar.gz
ENV PATH=$PATH:/usr/local/go/bin

RUN git clone https://github.com/dmwm/dasgoclient.git
WORKDIR dasgoclient
RUN go get github.com/dmwm/cmsauth
RUN go get github.com/dmwm/das2go
RUN go get github.com/vkuznet/x509proxy
RUN go get github.com/buger/jsonparser
RUN go get github.com/pkg/profile

RUN make build_all

RUN mv dasgoclient_amd64 /usr/local/go/bin/dasgoclient

#RUN python3 -m pip install git+https://gitlab.cern.ch/cp3-cms/CMSJMECalculators.git



ENTRYPOINT ["conda", "run", "--no-capture-output", "-n", "base"]


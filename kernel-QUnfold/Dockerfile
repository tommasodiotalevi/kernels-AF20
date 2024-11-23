FROM ghcr.io/comp-dev-cms-ita/kernel-root:v0.0.10-rc6

USER root

WORKDIR /usr/local/share/comp-dev-cms-ita

RUN pip install git+https://gitlab.cern.ch/RooUnfold/RooUnfold
RUN git clone --branch dask https://github.com/Quantum4HEP/QUnfold.git
WORKDIR /usr/local/share/comp-dev-cms-ita/QUnfold
RUN pip install -e .[gurobi]
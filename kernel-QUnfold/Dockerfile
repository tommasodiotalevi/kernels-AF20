FROM ghcr.io/comp-dev-cms-ita/kernel-root

USER root

WORKDIR /usr/local/share/comp-dev-cms-ita

RUN pip install git+https://gitlab.cern.ch/RooUnfold/RooUnfold
RUN git clone https://github.com/Quantum4HEP/QUnfold.git
WORKDIR /usr/local/share/comp-dev-cms-ita/QUnfold
RUN pip install -e .[gurobi]
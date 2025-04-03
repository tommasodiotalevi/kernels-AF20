FROM ghcr.io/comp-dev-cms-ita/kernel-coffea

RUN apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Europe/Zurich apt-get -y install \
    git-lfs ninja-build

RUN (echo cf_analysis; \
     echo cf_analysis; \
     echo CR; \
     echo cms_minimal; \
     echo False) | bash -c "$(curl -Ls https://raw.githubusercontent.com/columnflow/columnflow/master/create_analysis.sh)"

WORKDIR /usr/local/share/comp-dev-cms-ita/cf_analysis

SHELL ["/bin/bash", "-c"] 
ENV PATH=/usr/local/share/comp-dev-cms-ita/cf_analysis/data/software/venvs/cf_dev_9b04c75c/bin/:$PATH

RUN source setup.sh
RUN sleep 5
RUN tests/run_all

RUN echo "source /usr/local/share/comp-dev-cms-ita/cf_analysis/setup.sh" >> ~/.bashrc
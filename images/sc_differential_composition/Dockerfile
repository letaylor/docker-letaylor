FROM rocker/r-ver:3.6.1


############################ Setup: OS #########################################
# Set up locales properly
RUN apt-get update && \
    apt-get install --yes --no-install-recommends locales && \
    apt-get purge && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Use bash as default shell, rather than sh
ENV SHELL /bin/bash

# run critical system updates ... one could also use the artful-curl pack
RUN apt-get update && \
    apt-get install --yes --no-install-recommends \
        wget \
        bzip2 \
        ca-certificates \
        curl \
        git \
        zip \
        unzip \
        procps && \
    apt-get install --yes \
        libpng-dev \
        libcurl4-gnutls-dev \
        libssl-dev \
        libxml2-dev \
        libgit2-dev \
        zlib1g-dev \
        build-essential && \
    apt-get purge && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# set up a user, to avoid running containers as root
ENV NB_USER container_user
ENV HOME /home/${NB_USER}
RUN adduser --disabled-password \
    --gecos "Default user" \
    ${NB_USER}

# set up the user directory structure
RUN mkdir ${HOME}/tmp && \
    mkdir ${HOME}/bin && \
    mkdir ${HOME}/analysis
ENV PATH ${HOME}/bin:$PATH
################################################################################


############################ Setup: conda ######################################
# install conda
USER root
ENV CONDA_FILE https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
RUN echo "export PATH=${HOME}/conda/bin:$PATH" > /etc/profile.d/conda.sh && \
    wget --quiet ${CONDA_FILE} -O /tmp/miniconda.sh && \
    /bin/bash /tmp/miniconda.sh -b -p ${HOME}/conda && \
    rm /tmp/miniconda.sh && \
    ${HOME}/conda/bin/conda clean -atipy
ENV PATH ${HOME}/conda/bin:$PATH

# install mamba
RUN conda install mamba -n base -c conda-forge

# alternative conda install (instead of the manual install from above)
# FROM continuumio/miniconda3:latest
################################################################################


############################ Setup: non-conda tools ############################
# download things to tmp
WORKDIR ${HOME}/tmp

# Example: install GCTA
#RUN wget --quiet \
#    https://cnsgenomics.com/software/gcta/gcta_1.92.1beta5.zip && \
#    unzip gcta_1.92.1beta5.zip && \
#    chmod 755 gcta_1.92.1beta5/gcta64 && \
#    mv gcta_1.92.1beta5/gcta64 /bin/gcta64 && \
#    rm gcta_1.92.1beta5.zip && \
#    rm -r gcta_1.92.1beta5
################################################################################


############################ Setup: conda environment ##########################
# make sure conda files are owned by user
RUN chown -R ${NB_USER}:${NB_USER} ${HOME}
USER ${NB_USER}

# download the conda environment OR copy from local directory
COPY environment.yml ${HOME}/tmp/environment.yml

# install conda environment
# NOTE: conda clean -atipy
#   removes everything w/o confirmation (leaves all environments functional).
RUN mamba config --set always_yes yes --set changeps1 no && \
    mamba update mamba --yes && \
    mamba env update -v -n root --file ${HOME}/tmp/environment.yml && \
    mamba list --name root && \
    mamba clean -atipy && \
    rm ${HOME}/tmp/environment.yml

# export the conda path to force container_env since .bashrc necessarily
# sourced when when the image is used
# ENV PATH ${HOME}/conda/envs/container_env/bin:$PATH
# ENV CONDA_DEFAULT_ENV container_env
# ENV CONDA_PREFIX ${HOME}/conda/envs/container_env
################################################################################


############################ Setup: R packages #################################
# make sure HOME files are owned by user
#RUN chown -R ${NB_USER}:${NB_USER} ${HOME}
#USER ${NB_USER}

#RUN mkdir ${HOME}/tmp/renv && \
#    echo 'source("renv/activate.R")' > ${HOME}/tmp/run.R && \
#    echo 'renv::restore()' >> ${HOME}/tmp/run.R

# copy renv information from local directory
#COPY renv.lock ${HOME}/tmp/renv.lock
#COPY renv/activate.R ${HOME}/tmp/renv/activate.R

#RUN Rscript ${HOME}/tmp/run.R
################################################################################


############################ Setup: cleanup ####################################
# clear tmp if there is anything in there...
USER root
RUN rm -rf ${HOME}/tmp
RUN rm -rf /tmp/*
################################################################################


############################ Deployment ########################################
# set wd to user home
WORKDIR ${HOME}

# set bash as the default entry point
# NOTE: for Google Pipelines APIv2, docker does not execute if entrypoint is set
#ENTRYPOINT [ "/bin/bash", "-c" ]

# define default command as an alternative to ENTRYPOINT
# NOTE: for Google Pipelines APIv2, docker does not execute if CMD is set
#CMD ["/bin/bash"]

# set the user
# NOTE: for Snakemake + Kubernetes we get an error if this != root
# NOTE: for adding a monitoring script with cromwell, the monitoring script is
#       copied into the docker image and needs to be chmod to run. Permission
#       errors arise if not root.
USER ${NB_USER}
################################################################################

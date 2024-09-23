ARG REGISTRY=quay.io
ARG OWNER=jupyter
ARG BASE_CONTAINER=$REGISTRY/$OWNER/minimal-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Roman Dvorak <dvorak@ujf.cas.cz>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]


USER root

RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    # for cython: https://cython.readthedocs.io/en/latest/src/quickstart/install.html
    build-essential \
    # for latex labels
    cm-super \
    dvipng \
    # for matplotlib anim
    ffmpeg \ 
    sudo sshfs python3-pip gosu fuse fuse3 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*


RUN echo "jovyan ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN groupadd fuse && usermod -a -G fuse jovyan

# Install Python 3 packages
RUN sudo pip3 install \
    'astropy' \
    'altair' \
    'beautifulsoup4' \
    'bokeh' \
    'bottleneck' \
    'cloudpickle' \
    #'conda-forge::blas=*=openblas' \
    'cython' \
    'dask' \
    'dill' \
    'h5py' \
    'ipympl' \
    'ipywidgets' \
    'jupyterlab-git' \
    'matplotlib' \
    'numba' \
    'numexpr' \
    'openpyxl' \
    'pandas' \
    'patsy' \
    'protobuf' \
#    'pytables' \
    'pyarrow' \
    'scikit-image' \
    'scikit-learn' \
    'scipy' \
    'seaborn' \
    'sqlalchemy' \
    'statsmodels' \
    'sympy' \
    'widgetsnbextension' \
    'xlrd' \
     --break-system-packages && \
#    mamba clean --all -f -y && \
#    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

USER ${NB_UID}

# Install facets package which does not have a `pip` or `conda-forge` package at the moment
WORKDIR /tmp
RUN git clone https://github.com/PAIR-code/facets && \
    jupyter nbclassic-extension install facets/facets-dist/ --sys-prefix && \
    rm -rf /tmp/facets && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Import matplotlib the first time to build the font cache
# RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
#     fix-permissions "/home/${NB_USER}"

USER ${NB_UID}

WORKDIR "${HOME}"

########################################################################
# The course-specific docker file
########################################################################

FROM jgomezdans/conda-base as geog0666


LABEL maintainer="Jose Gomez-dans <j.gomez-dans@ucl.ac.uk"
ARG NB_USER="jovyan"
ARG NB_UID="1000"
ARG NB_GID="100"

# Fix DL4006
SHELL ["/bin/bash", "-o", "pipefail", "-c"]


# name your environment
ARG conda_env=geog0666
ENV PATH=$CONDA_DIR/bin:$PATH \
    HOME=$HOME

RUN rm -rf "${HOME}"/tmp/ && mkdir -p "${HOME}"/tmp/

# Switch back to jovyan to avoid accidental container runs as root
USER $NB_UID

WORKDIR "${HOME}"


COPY Docker/environment.yml "${HOME}"/tmp/
RUN cd "${HOME}"/tmp/ && \
     conda env create -p $CONDA_DIR/envs/$conda_env -f environment.yml && \
     conda clean --all -f -y \
    && rm -rf "${HOME}"/tmp/environment.yml \
    && find /opt/conda/ -follow -type f -name '*.a' -delete \
    && find /opt/conda/ -follow -type f -name '*.pyc' -delete \
    && find /opt/conda/ -follow -type f -name '*.js.map' -delete



# create Python 3.x environment and link it to jupyter
RUN $CONDA_DIR/envs/${conda_env}/bin/python -m ipykernel install --user --name=${conda_env} && \
    fix-permissions $CONDA_DIR && \
    fix-permissions "${HOME}"

ENV PATH=$CONDA_DIR/envs/${conda_env}/bin:$PATH

ENV CONDA_DEFAULT_ENV=${conda_env}


RUN python -m pip install jupyterthemes && \
    python -m pip install --upgrade jupyterthemes && \
    python -m pip install jupyter_contrib_nbextensions && \
    jupyter contrib nbextension install --user && \
    rm -rf ${HOME}/.cache/pip
# enable the Nbextensions
RUN jupyter nbextension enable contrib_nbextensions_help_item/main && \
    jupyter nbextension enable autosavetime/main && \
    jupyter nbextension enable codefolding/main && \
    jupyter nbextension enable code_font_size/code_font_size && \
    jupyter nbextension enable code_prettify/code_prettify && \
    jupyter nbextension enable collapsible_headings/main && \
    jupyter nbextension enable comment-uncomment/main && \
    jupyter nbextension enable equation-numbering/main && \
    jupyter nbextension enable execute_time/ExecuteTime && \
    jupyter nbextension enable gist_it/main && \
    jupyter nbextension enable hide_input/main && \
    jupyter nbextension enable spellchecker/main && \
    jupyter nbextension enable toc2/main && \
    jupyter nbextension enable toggle_all_line_numbers/main && \
    jupyter nbextension enable exercise2/main  && \
    rm -rf "${HOME}"/.cache/pip
    


RUN mkdir -p "${HOME}"/notebooks
RUN fix-permissions "${HOME}"/notebooks/
COPY notebooks/* "${HOME}"/notebooks/
RUN jupyter trust notebooks/*ipynb 



WORKDIR $HOME

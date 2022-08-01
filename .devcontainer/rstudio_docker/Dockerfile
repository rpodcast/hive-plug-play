##################################################################################################################################################################
# Adapted from following:
# - Rocker RStudio container using new versioned paradigm: https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/Dockerfile_rstudio_4.0.2
# - license: GPLV2
##################################################################################################################################################################

FROM rocker/r-ver:4.1.3

ARG S6_VERSION
ARG RSTUDIO_VERSION
ARG QUARTO_VERSION
ARG DB_PASSWORD="shiny"

ENV S6_VERSION=$S6_VERSION
ENV RSTUDIO_VERSION=$RSTUDIO_VERSION
ENV PATH=/usr/lib/rstudio-server/bin:$PATH
ENV QUARTO_VERSION=$QUARTO_VERSION
ENV DB_PASSWORD=$DB_PASSWORD

# key dependencies for certain R packages
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends software-properties-common curl wget libssl-dev libxml2-dev libsodium-dev imagemagick libmagick++-dev libgit2-dev libssh2-1-dev zlib1g-dev librsvg2-dev libudunits2-dev libcurl4-openssl-dev python3-pip pandoc libzip-dev libfreetype6-dev libfontconfig1-dev tk libpq5 libxt6 openssh-client openssh-server libglpk40 \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/*

RUN /rocker_scripts/install_rstudio.sh
RUN /rocker_scripts/install_pandoc.sh
RUN /rocker_scripts/install_quarto.sh

# install key dependencies of certain packages that could be installed later
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends libxml2-dev libsodium-dev libssl-dev imagemagick libmagick++-dev libgit2-dev libssh2-1-dev zlib1g-dev librsvg2-dev libudunits2-dev libfontconfig1-dev libfreetype6-dev curl 

RUN echo "RENV_PATHS_CACHE=/renv/cache" >> /usr/local/lib/R/etc/Renviron

# [Optional] Uncomment this section to add addtional system dependencies needed for project
# RUN apt-get update \
#     && export DEBIAN_FRONTEND=noninteractive \
#     && apt-get -y install --no-install-recommends ---packages list----

# [Optional] Uncomment this section to install additional R packages.
RUN install2.r --error --skipinstalled --ncpus -1 renv remotes httpgd DBI RPostgres dplyr dbplyr httr2 fs purrr tibble tidyr shiny dm

# [Optional] Uncomment this section to add shinycannon to the container
RUN curl -L https://github.com/rstudio/shinycannon/releases/download/v1.1.3/shinycannon_1.1.3-dd43f6b_amd64.deb -o /tmp/shinycannon.deb
RUN apt-get update \
  && apt-get -y install --no-install-recommends default-jre-headless \
  && apt-get -y install ./tmp/shinycannon.deb

  # install radian via python and pip3
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends python3-setuptools

RUN pip3 install radian config psycopg2-binary requests aiohttp jsonrpcserver

RUN apt-get update \
  && apt-get -y install --no-install-recommends postgresql-all

EXPOSE 8787

CMD ["/init"]

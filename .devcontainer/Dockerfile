FROM ubuntu:22.04 AS codeql_base

# # tzdata install needs to be non-interactive
# ENV DEBIAN_FRONTEND=noninteractive

ARG USERNAME=root
ARG CODEQL_HOME=/usr/local/codeql-home

# create user, install/update basics and python
RUN mkdir ${CODEQL_HOME}

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        software-properties-common \
        curl \
        git \
        git-lfs \
        build-essential \
        unzip \
        apt-transport-https \
        python3.10 \
        python3-venv \
        python3-pip \
        python3-setuptools \
        python3-dev \
        python-is-python3 \
        gnupg \
        g++ \
        make \
        gcc \
        apt-utils \
        && \
        apt-get clean \
        docker-cli

# Install Go
ARG GOVER=1.21.5
RUN cd /tmp && \
    arch=$(dpkg --print-architecture) && \
    curl -OL https://golang.org/dl/go${GOVER}.linux-${arch}.tar.gz && \
    tar -C /usr/local -xvf go${GOVER}.linux-${arch}.tar.gz && \
    rm -rf /tmp/go${GOVER}.linux-${arch}.tar.gz

# Install .NET SDK
ARG DOTNETVER=8.0
RUN cd /tmp && \
    curl -OL https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    apt-get update && \
    apt-get install -y --no-install-recommends dotnet-sdk-${DOTNETVER} && \
    rm packages-microsoft-prod.deb

# Install JDK
ARG JDKVER=21
RUN apt-get install -y --no-install-recommends openjdk-${JDKVER}-jre-headless

# Clean up
RUN apt-get clean && apt-get autoremove

# Install CodeQL
USER ${USERNAME}
ARG CODEQLVER=2.19.1
RUN cd /tmp && \
    curl -OL https://github.com/github/codeql-action/releases/download/codeql-bundle-v${CODEQLVER}/codeql-bundle-linux64.tar.gz && \
    tar -xvf /tmp/codeql-bundle-linux64.tar.gz --directory ${CODEQL_HOME} && \
    rm /tmp/codeql-bundle-linux64.tar.gz

    
RUN echo 'alias act_on="act --var-file act.variables --secret-file my.secrets -e act.event.json -s GITHUB_TOKEN=\"$(gh auth token)\" "' >> ~/.bashrc 
RUN echo 'alias act_off="act --action-offline-mode --var-file act.variables --secret-file my.secrets -e act.event.json -s GITHUB_TOKEN=\"$(gh auth token)\" "' >> ~/.bashrc

ENV CODEQL_HOME=${CODEQL_HOME}
ENV PYTHONIOENCODING=utf-8
ENV PATH="/usr/local/go/bin:${PATH}"
ENV PATH="${CODEQL_HOME}/codeql:${PATH}"
USER ${USERNAME}

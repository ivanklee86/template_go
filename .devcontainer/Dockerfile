# See here for image contents: https://github.com/microsoft/vscode-dev-containers/blob/main/containers/go/.devcontainer/Dockerfile
ARG VARIANT="debian-12"
FROM mcr.microsoft.com/devcontainers/base:${VARIANT}

# Install dependencies.
# [Choice] Node.js version: none, lts/*, 16, 14, 12, 10
ARG NODE_VERSION="none"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

# Install standard packages
RUN apt-get clean && \
    apt-get update && \
        # General purpose tools
    apt-get install -y \
        # Python
        libsasl2-dev libldap2-dev libssl-dev libsnmp-dev libffi-dev \
        libncurses-dev libsqlite3-dev libbz2-dev libreadline-dev liblzma-dev tzdata

# Set general-purpose environment variables.
ENV HOME="/home/vscode"
ENV UV_CACHE_DIR="/home/vscode/.uv_cache"

# Switch to non-root user and home directory.
USER vscode
WORKDIR /home/vscode

# Install and configure Golang
RUN wget "https://dl.google.com/go/$(curl https://go.dev/VERSION?m=text | head -n1).linux-amd64.tar.gz" && \
    tar -zxf go*.linux-amd64.tar.gz && \
    rm go*.linux-amd64.tar.gz

RUN mkdir -p /home/vscode/go-path
RUN mkdir -p /home/vscode/go
ENV GOPATH=/home/vscode/go-path
ENV GOROOT=/home/vscode/go
ENV PATH=${PATH}:/usr/local/bin:$GOROOT/bin:$GOPATH/bin
ENV GOTOOLCHAIN=auto

# Install and configure Python
COPY --from=ghcr.io/astral-sh/uv:debian /usr/local/bin/uv /usr/local/bin
COPY --from=ghcr.io/astral-sh/uv:debian /usr/local/bin/uvx /usr/local/bin
ARG PYTHON_VERSION=3.13
RUN uv python install ${PYTHON_VERSION}
ENV UV_LINK_MODE=copy

# Install tools
RUN sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d

# Go
RUN go install github.com/jstemmer/go-junit-report/v2@latest
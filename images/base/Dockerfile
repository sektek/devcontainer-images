ARG VARIANT=plucky
ARG NODE_VERSION=22
ARG PYTHON_VERSION=3

FROM node:${NODE_VERSION} AS node-stage
FROM python:${PYTHON_VERSION} AS python-stage
FROM grafana/logcli:latest AS logcli
FROM grafana/tempo-cli:latest AS tempo-cli

FROM buildpack-deps:${VARIANT}

COPY --from=logcli /usr/bin/logcli /usr/bin/logcli
COPY --from=tempo-cli /tempo-cli /usr/bin/tempo-cli

COPY --from=node-stage /usr/local /usr/local
COPY --from=node-stage /opt /opt

COPY --from=python-stage /usr/local /usr/local

RUN mkdir -p -m 755 /etc/apt/keyrings \
    && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    && cat $out | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	  && chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    bash-completion \
    ca-certificates \
    cmake \
    dialog \
    gawk \
    gnupg2 \
    gh \
    htop \
    iproute2 \
    iputils-ping \
    jq \
    less \
    libbluetooth-dev \
    libstdc++-11-dev \
    lsof \
    manpages \
    manpages-dev \
    net-tools \
    postgresql-client \
    psmisc \
    redis-tools \
    strace \
    sudo \
    telnet \
    tk-dev \
    tmux \
    uuid-dev \
    vim-tiny \
    zsh \
    ; \
    apt-get -y upgrade --no-install-recommends \
    ; \
    apt-get autoremove -y \
    ; \
    rm -rf /var/lib/apt/lists/*

ARG USERNAME=dev
ARG NPM_GLOBAL=/usr/local/share/npm-global

RUN \
    # Configure global npm install location, use group to adapt to UID/GID changes
    if ! cat /etc/group | grep -e "^npm:" > /dev/null 2>&1; then groupadd -r npm; fi \
    && if ! cat /etc/passwd | grep -e "^${USERNAME}:" > /dev/null 2>&1; then useradd -m ${USERNAME}; fi \
    && chsh -s /bin/zsh ${USERNAME} \
    && usermod -a -G npm ${USERNAME} \
    && umask 0002 \
    && mkdir -p ${NPM_GLOBAL} \
    && touch /usr/local/etc/npmrc \
    && chown ${USERNAME}:npm ${NPM_GLOBAL} /usr/local/etc/npmrc \
    && chmod g+s ${NPM_GLOBAL} \
    && npm config -g set prefix ${NPM_GLOBAL} \
    && su ${USERNAME} -c "npm config -g set prefix ${NPM_GLOBAL}" \
    # Install eslint
    && su ${USERNAME} -c "umask 0002 && npm install -g eslint" \
    && npm cache clean --force > /dev/null 2>&1

RUN echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

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

ARG USERNAME=dev
ARG NPM_GLOBAL=/usr/local/share/npm-global

RUN mkdir -p -m 755 /etc/apt/keyrings \
    && out=$(mktemp) && wget -nv -O$out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    && cat $out | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	  && chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
    && sed -i '/path-exclude=\/usr\/share\/man/s/^/#/' /etc/dpkg/dpkg.cfg.d/excludes \
    && apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    bash-completion \
    ca-certificates \
    cmake \
    dialog \
    eza \
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
    locales \
    man-db \
    manpages \
    manpages-dev \
    manpages-posix \
    manpages-posix-dev \
    net-tools \
    postgresql-client \
    psmisc \
    redis-tools \
    strace \
    sudo \
    telnet \
    tk-dev \
    tmux \
    unminimize \
    uuid-dev \
    vim-tiny \
    zsh \
    ; \
    apt-get -y install --reinstall git-man \
    ; \
    mv /usr/bin/man.REAL /usr/bin/man \
    ; \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen \
    ; \
    mandb -c \
    ; \
    apt-get -y upgrade --no-install-recommends \
    ; \
    apt-get purge -y imagemagick imagemagick-6-common \
    apt-get autoremove -y \
    ; \
    rm -rf /var/lib/apt/lists/*

ARG GK_VERSION=3.1.44
ARG BAT_VERSION=0.26.0
ARG TARGETARCH

RUN wget https://github.com/gitkraken/gk-cli/releases/download/v${GK_VERSION}/gk_${GK_VERSION}_linux_${TARGETARCH}.deb \
    && wget https://github.com/sharkdp/bat/releases/download/v${BAT_VERSION}/bat_${BAT_VERSION}_${TARGETARCH}.deb \
    && dpkg -i gk_${GK_VERSION}_linux_${TARGETARCH}.deb \
    && dpkg -i bat_${BAT_VERSION}_${TARGETARCH}.deb \
    && rm gk_${GK_VERSION}_linux_${TARGETARCH}.deb \
    && rm bat_${BAT_VERSION}_${TARGETARCH}.deb

ARG USERNAME=dev
ARG NPM_GLOBAL=/usr/local/share/npm-global

RUN if cat /etc/passwd | grep -e "^ubuntu:" > /dev/null 2>&1; then userdel -f ubuntu; fi \
    && if cat /etc/group | grep -e "^ubuntu:" > /dev/null 2>&1; then groupdel ubuntu; fi \
    && if ! cat /etc/group | grep -e "^npm:" > /dev/null 2>&1; then groupadd -r npm; fi \
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
    && npm cache clean --force > /dev/null 2>&1 \
    && echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

USER ${USERNAME}

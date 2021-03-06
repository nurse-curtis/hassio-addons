FROM homeassistant/amd64-base:3.12

# Setup base
ARG BUILD_ARCH="amd64"
ARG CLI_VERSION="4.3.0"
ARG LIBWEBSOCKETS_VERSION="v3.2.2"
ARG TTYD_VERSION="1.6.0"
RUN \
	apk update && \
	apk add --no-cache \
        bsd-compat-headers \
        build-base \
        cmake \
        json-c-dev \
        libuv-dev \
        openssl-dev \
        zlib-dev \
        bash-completion \
        pulseaudio-utils \
        alsa-plugins-pulse \
        git \
        libuv \
        mosquitto-clients \
        nano \
        openssh \
        pwgen \
        tmux \
        vim \
	rsync \
	mariadb-client \
	curl \
	python3 \
	nmap \
	docker \
	htop \
	bind-tools \
	mc \
	device-mapper-libs
RUN \
    sed -i "s/ash/bash/" /etc/passwd \
    \
    && git clone --branch "${LIBWEBSOCKETS_VERSION}" --depth=1 \
        https://github.com/warmcat/libwebsockets.git /tmp/libwebsockets \
    \
    && mkdir -p /tmp/libwebsockets/build \
    && cd /tmp/libwebsockets/build \
    && cmake .. \
        -DCMAKE_BUILD_TYPE=MinSizeRel \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_VERBOSE_MAKEFILE=TRUE \
        -DLWS_IPV6=ON \
        -DLWS_STATIC_PIC=ON \
        -DLWS_UNIX_SOCK=OFF \
        -DLWS_WITH_LIBUV=ON \
        -DLWS_WITH_SHARED=ON \
        -DLWS_WITHOUT_TESTAPPS=ON \
    && make \
    && make install \
    \
    && git clone --branch master --single-branch \
        https://github.com/tsl0922/ttyd.git /tmp/ttyd \
    && git -C /tmp/ttyd checkout "${TTYD_VERSION}" \
    \
    && mkdir -p /tmp/ttyd/build \
    && cd /tmp/ttyd/build \
    && cmake .. \
        -DCMAKE_BUILD_TYPE=MinSizeRel \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_VERBOSE_MAKEFILE=TRUE \
    && make \
    && make install \
    \
    && rm -f -r \
        /root/.cache \
        /root/.cmake \
        /tmp/*

# Add YAML highlighting for nano
ADD https://raw.githubusercontent.com/scopatz/nanorc/master/yaml.nanorc /usr/share/nano/yaml.nanorc
RUN sed -i 's/^#[[:space:]]*\(include "\/usr\/share\/nano\/\*\.nanorc".*\)/\1/' /etc/nanorc

# Home Assistant CLI
ARG BUILD_ARCH
ARG CLI_VERSION
RUN curl -Lso /usr/bin/ha \
        "https://github.com/home-assistant/cli/releases/download/4.3.0/ha_amd64" \
    && chmod a+x /usr/bin/ha \
    && /usr/bin/ha completion > /usr/share/bash-completion/completions/ha

# Copy data
COPY rootfs /

ARG UV_VERSION=0.13.0
FROM ghcr.io/astral-sh/uv:${UV_VERSION} AS uv_source

FROM debian:trixie-slim

RUN apt-get update && apt-get install -y --no-install-recommends curl ca-certificates \
    && curl -fsSL https://raw.githubusercontent.com/raspberrypi/rpi-image-gen/1f5851b24a6ac25b91edfaed5f2b59e770d0ac9e/keydir/raspberrypi-archive-keyring.gpg -o /usr/share/keyrings/raspberrypi-archive-keyring.gpg \
    && echo "3a29901549ae65a910de13a32551e6eea7f45ba1fd70f0cb67e5c70d98488071  /usr/share/keyrings/raspberrypi-archive-keyring.gpg" | sha256sum -c - \
    && echo "deb [signed-by=/usr/share/keyrings/raspberrypi-archive-keyring.gpg] https://archive.raspberrypi.org/debian trixie main" > /etc/apt/sources.list.d/raspi.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends raspberrypi-archive-keyring

ARG PYTHON_VERSION=3.13
RUN --mount=type=cache,target=/var/cache/apt \
    apt-get update && apt-get install -y --no-install-recommends \
    wget \
    unzip \
    git-core \
    openssh-client \
    build-essential \
    swig \
    liblgpio-dev \
    python${PYTHON_VERSION} \
    python${PYTHON_VERSION}-dev \
    python3-setuptools \
    python3-picamera2 \
    python3-rpi.gpio \
    hailort \
    python3-hailort \
    hailo-tappas-core \
    vim \
    htop \
    zsh \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/tmp/* /var/lib/apt/lists/*

RUN rm /usr/lib/python${PYTHON_VERSION}/EXTERNALLY-MANAGED
RUN update-alternatives --install /usr/bin/python python /usr/bin/python${PYTHON_VERSION} 1 \
    && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python${PYTHON_VERSION} 1

ENV UV_LINK_MODE=copy
ENV UV_SYSTEM_PYTHON=1
COPY --from=uv_source /uv /uvx /bin/

WORKDIR /tmp

COPY ./requirements.txt requirements.txt
RUN --mount=type=cache,target=/root/.cache \
    uv pip install -r requirements.txt

RUN rm -rf /tmp/*
WORKDIR /workspace

ENV HAILORT_LOGGER_PATH=NONE
ENV SHELL=/usr/bin/zsh
RUN sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

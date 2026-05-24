FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
  curl wget git vim nano \
  python3 python3-pip \
  unzip software-properties-common \
  build-essential ca-certificates openssh-client \
  && rm -rf /var/lib/apt/lists/*

# Go
RUN add-apt-repository ppa:longsleep/golang-backports -y \
  && apt-get update \
  && apt-get install -y golang-go

# Node
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs

# Yarn
RUN npm install -g yarn

# Claude Code
USER root
RUN npm install -g @anthropic-ai/claude-code

RUN useradd -ms /bin/bash devuser
USER devuser
WORKDIR /home/devuser

CMD [ "bash" ]

FROM debian:bullseye

ARG GO_VERSION=1.18.2
ARG NODE_VERSION=16.15.0
ARG SRC_MODULE=github.com/mkaiho/ecs-sample

RUN apt-get update -y && apt-get install -y \
  curl gcc make git zip unzip jq openssl
RUN useradd -m "devuser"

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
  && unzip awscliv2.zip \
  && rm awscliv2.zip \
  && ./aws/install \
  && rm -r ./aws \
&& curl -L -o direnv https://github.com/direnv/direnv/releases/download/v2.29.0/direnv.linux-amd64 \
  && chmod +x direnv \
  && mv direnv /usr/local/bin/ \
  && echo 'eval "$(direnv hook bash)"' >> ~/.bashrc

RUN mkdir -p /go/src/$SRC_MODULE && chmod -R 777 /go
RUN chsh devuser -s /bin/bash

USER devuser

WORKDIR /go/src/$SRC_MODULE

RUN git clone https://github.com/anyenv/anyenv ~/.anyenv \
&& echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bash_profile \
&& echo 'eval "$(anyenv init -)"' >> ~/.bash_profile \
&& . ~/.bash_profile \
&& echo y | anyenv install --init \
&& anyenv install nodenv \
&& anyenv install goenv \
&& . ~/.bash_profile \
&& nodenv install $NODE_VERSION \
&& nodenv global $NODE_VERSION \
&& npm install -g aws-cdk aws-cdk-local \
&& goenv install $GO_VERSION \
&& goenv global $GO_VERSION \
&& echo 'alias aws-local="aws --endpoint http://localstack:4566"' >> ~/.bashrc

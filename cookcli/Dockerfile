FROM debian:latest AS build-ui
USER root
WORKDIR /

RUN apt-get update
RUN apt-get install -y --no-install-recommends --no-install-suggests \
  git \
  nodejs \
  npm \
  unzip

RUN git clone https://github.com/cooklang/cookcli.git --branch v0.8.0 --single-branch /app

WORKDIR /app

RUN cd ui && npm install && npm run build

FROM rust:latest AS build-cli
USER root
WORKDIR /

COPY --from=build-ui /app /app

WORKDIR /app

RUN cargo build --release

FROM debian:latest AS deploy
USER root
WORKDIR /app

COPY --from=build-cli /app/target/release/cook /app/cook
RUN chmod +x cook
RUN mkdir /var/cookbook

EXPOSE 9080

CMD ["/app/cook", "server", "/var/cookbook", "--host"]
# CMD ["/bin/sh"]

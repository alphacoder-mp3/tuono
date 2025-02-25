# ARGs obtained from the compose.yml file

ARG BASE_OS=bookworm
ARG RUST_VERSION=1.83

FROM rust:${RUST_VERSION}-${BASE_OS}

# Nodejs installation

RUN ["mkdir", "/usr/local/node"]
WORKDIR /usr/local/node

## Retrieves the targeted version of Node in the container

COPY .nvmrc /tmp/.nvmrc

## Download the Node.js standelone binary archive && extract it && clean files

RUN ["/bin/sh", "-c", "NODE_VERSION=$(cat /tmp/.nvmrc | tr -d '\r') && \
    wget https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.xz && \
    tar xf node-v${NODE_VERSION}-linux-x64.tar.xz --strip-components 1 && \
    rm node-v${NODE_VERSION}-linux-x64.tar.xz /tmp/.nvmrc"]

## Ensure Node.js binaries are available in the PATH

ENV PATH /usr/local/node/bin:$PATH

# cargo-watch installation (for Rust live-reloading during dev')

RUN ["cargo", "install", "cargo-watch"]

# global configuration done ! let's focus on the project itself

## Copy project files into the container (excepted files/dir declared in .dockerignore) to /tuono directory

WORKDIR /tuono
COPY . .

# pnpm installation based on version found in package.json

RUN ["corepack", "enable"]
RUN ["corepack", "install"]

## Install Node.js dependencies (defined in package.json)

RUN ["pnpm", "install", "--frozen-lockfile"]

## Compile and add the directory with compiled binaries to the PATH for direct access

RUN ["cargo", "build"]
ENV PATH=/tuono/target/debug:$PATH
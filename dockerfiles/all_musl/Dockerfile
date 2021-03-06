FROM debian:stretch

MAINTAINER Liu Chong <mail@clojure.cn>

WORKDIR /root

# common packages
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
    ca-certificates curl file \
    build-essential \
    autoconf automake autotools-dev libtool xutils-dev && \
    rm -rf /var/lib/apt/lists/*

ENV SSL_VERSION=1.0.2l

RUN curl https://www.openssl.org/source/openssl-$SSL_VERSION.tar.gz -O && \
    tar -xzf openssl-$SSL_VERSION.tar.gz && \
    cd openssl-$SSL_VERSION && ./config && make depend && make install && \
    cd .. && rm -rf openssl-$SSL_VERSION*

ENV OPENSSL_LIB_DIR=/usr/local/ssl/lib \
    OPENSSL_INCLUDE_DIR=/usr/local/ssl/include \
    OPENSSL_STATIC=1

# install all 3 toolchains
RUN curl https://sh.rustup.rs -sSf | \
    sh -s -- --default-toolchain stable -y && \
    /root/.cargo/bin/rustup update beta && \
    /root/.cargo/bin/rustup update nightly

ENV PATH=/root/.cargo/bin:$PATH

# musl tools
RUN apt-get update && \
    apt-get install --no-install-recommends -y \
    musl-tools && \
    rm -rf /var/lib/apt/lists/*

# install all 3 musl targets
RUN rustup target add x86_64-unknown-linux-musl \
    --toolchain stable-x86_64-unknown-linux-gnu && \
    rustup target add x86_64-unknown-linux-musl \
    --toolchain beta-x86_64-unknown-linux-gnu && \
    rustup target add x86_64-unknown-linux-musl \
    --toolchain nightly-x86_64-unknown-linux-gnu

# make default target to musl
RUN mkdir /.cargo && \
    echo "[build]\ntarget = \"x86_64-unknown-linux-musl\"" > /.cargo/config

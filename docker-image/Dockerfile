FROM amazonlinux:2.0.20221004.0

# dependencies
RUN yum -y install \
    gcc \
    gmp \
    gmp-devel \
    gzip \
    make \
    perl \
    pkgconfig \
    postgresql-devel \
    tar \
    which \
    xz \
    zlib \
    zlib-devel \
    && yum clean all \
    && rm -rf /var/cache/yum

# stack
ARG STACK=2.9.1
ARG ARCH=x86_64-static

RUN set -eux; \
    cd /tmp; \
    curl -sSL https://github.com/commercialhaskell/stack/releases/download/v${STACK}/stack-${STACK}-linux-${ARCH}.tar.gz -o stack.tar.gz; \
    tar -xf stack.tar.gz -C /usr/local/bin --strip-components=1 "stack-$STACK-linux-${ARCH}/stack"; \
    stack config set system-ghc --global true; \
    stack config set install-ghc --global false; \
    # workaround for https://github.com/commercialhaskell/stack/issues/3348
    echo "allow-different-user: true" >> /root/.stack/config.yaml; \
    rm -rf /tmp/*; \
    stack --version;

# ghc
ARG GHC_VER=9.2.4
RUN set -eux; \
    cd /tmp; \
    curl -sSL https://downloads.haskell.org/~ghc/${GHC_VER}/ghc-${GHC_VER}-x86_64-fedora27-linux.tar.xz -o ghc.tar.xz; \
    tar xf ghc.tar.xz; \
    cd ghc-${GHC_VER}; \
    ./configure --prefix "/opt/ghc/${GHC_VER}"; \
    make install; \
    # remove profiling support to save space
    find "/opt/ghc/${GHC_VER}/" \( -name "*_p.a" -o -name "*.p_hi" \) -type f -delete; \
    rm -rf "/opt/ghc/${GHC_VER}/share/"; \
    rm -rf /tmp/*; \
    "/opt/ghc/${GHC_VER}/bin/ghc" --version

ENV PATH /root/.local/bin:/opt/ghc/9.2.4/bin:$PATH


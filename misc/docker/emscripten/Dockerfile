FROM apiaryio/emcc:1.38.11

RUN apt-get -y update
RUN apt-get install -y g++ ccache openssl cmake gperf make git libssl-dev libreadline-dev zlib1g zlib1g-dev wget

WORKDIR /

#             Patched tdlib; commit 2f7b563
RUN git clone https://github.com/feelfreelinux/td.git

SHELL ["/bin/bash", "-c"]

WORKDIR /td
RUN mkdir build
WORKDIR build
RUN cmake -DCMAKE_BUILD_TYPE=Release ..
RUN make -j 2
# RUN cmake --build . --target prepare_cross_compiling
RUN rm -rf *

WORKDIR /

# ENV OPENSSL openssl-1.0.2a
# ENV OPENSSL openssl-1.0.2p
# ENV OPENSSL openssl-1.1.0i
ENV OPENSSL openssl-1.1.0f
ENV EXT tar.gz
RUN wget https://www.openssl.org/source/${OPENSSL}.${EXT} && tar -xvzf ${OPENSSL}.${EXT}

WORKDIR ${OPENSSL}
RUN echo "Compiling OpenSSL..." \
  && emconfigure ./Configure no-asm no-ssl3 no-comp no-hw no-engine no-shared no-dso --openssldir=built linux-generic32 \
  && sed -i 's/CROSS_COMPILE= \/emscripten\/em/CROSS_COMPILE= /g' Makefile \
  && sed -i "s/-D_REENTRANT -O3 -Wall/-D_REENTRANT -Wall/g" Makefile \
  && sed -i 's/-Wall -O3 -pthread/-Wall -pthread/g' Makefile \
  && emmake make -j 4

WORKDIR /td/build

ENV OPENSSL_ROOT_DIR /${OPENSSL}
ENV OPENSSL_INCLUDE_DIR ${OPENSSL_ROOT_DIR}/include
ENV OPENSSL_CRYPTO_LIBRARY ${OPENSSL_ROOT_DIR}/libcrypto.a
ENV OPENSSL_SSL_LIBRARY ${OPENSSL_ROOT_DIR}/libssl.a

RUN emmake bash \
  && emcmake cmake -DCMAKE_BUILD_TYPE=Release \
  -DOPENSSL_INCLUDE_DIR=${OPENSSL_INCLUDE_DIR} \
  -DOPENSSL_CRYPTO_LIBRARY=${OPENSSL_CRYPTO_LIBRARY} \
  -DOPENSSL_SSL_LIBRARY=${OPENSSL_SSL_LIBRARY} \
  .. \
  && emmake make -j 2

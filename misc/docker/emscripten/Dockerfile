# WIP

FROM apiaryio/emcc:1.38.11

# Install packages
RUN apt-get -y update \
  && apt-get install -y g++ ccache cmake make git wget sed gperf libssl-dev libreadline-dev zlib1g zlib1g-dev

# Info
RUN uname -a
RUN cat /etc/os-release

WORKDIR /
ENV OPENSSL_VER OpenSSL_1_1_0i

# Compile openssl

# Download openssl
RUN wget https://github.com/openssl/openssl/archive/${OPENSSL_VER}.tar.gz -O - \
  | tar xz

#./Configure no-ssl3 no-comp no-hw no-engine no-deprecated no-shared no-dso --openssldir=built linux-generic32
#./Configure no-asm no-comp no-hw no-engine no-deprecated no-dso shared --openssldir=built linux-generic32
RUN cd openssl-${OPENSSL_VER} \
  && emmake bash \
  && ./Configure no-asm shared --openssldir=built linux-generic32 \
  && sed -i 's/\/emscripten\/emcc/cc/g' Makefile \
  && sed -i 's/-Wall -O3 -pthread/-Wall -pthread/g' Makefile \
  && sed -i 's/\/emscripten\/em\/emscripten\/emar/emar/g' Makefile \
  && sed -i 's/CROSS_COMPILE= \/emscripten\/em/CROSS_COMPILE= /g' Makefile \
  && make -j 4 \
  && exit

# Compile tdlib

RUN cd / \
  && git clone https://github.com/tdlib/td.git /td

RUN cd /td \
  && mkdir native-build \
  && mkdir build \
  && cd /td/native-build \
  && cmake -DCMAKE_BUILD_TYPE=Release .. \
  && cmake --build . --target prepare_cross_compiling \
  && cd /td/build

WORKDIR /td/build

RUN cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_TOOLCHAIN_FILE=/emscripten/cmake/Modules/Platform/Emscripten.cmake \
  -DOPENSSL_INCLUDE_DIR=/openssl-${OPENSSL_VER}/include \
  -DOPENSSL_CRYPTO_LIBRARY=/openssl-${OPENSSL_VER}/libcrypto.so \
  -DOPENSSL_SSL_LIBRARY=/openssl-${OPENSSL_VER}/libssl.so \
  ..

RUN cmake --build . -- -j 2
# RUN make -j 2

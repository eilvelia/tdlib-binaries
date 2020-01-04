FROM alpine:3.11

RUN apk update
RUN apk add -U \
  openssl-dev openssl-libs-static zlib-dev zlib-static readline-dev readline-static \
  linux-headers musl gperf ccache cmake make git g++

WORKDIR /
RUN git clone https://github.com/tdlib/td.git /td

WORKDIR /td
RUN mkdir build

WORKDIR /td/build

RUN cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DOPENSSL_USE_STATIC_LIBS=1 \
  -DZLIB_LIBRARY=/lib/libz.a \
  ..

RUN cmake --build .

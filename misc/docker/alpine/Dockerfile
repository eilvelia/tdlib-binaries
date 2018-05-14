FROM alpine:3.7

RUN apk update
RUN apk add -U alpine-sdk g++ ccache openssl openssl-dev cmake gperf make git readline-dev ninja

RUN openssl version
RUN uname -a

WORKDIR /
RUN git clone https://github.com/tdlib/td.git /td

WORKDIR /td
RUN mkdir build
WORKDIR build
RUN cmake -DCMAKE_BUILD_TYPE=Release ..
#RUN cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
#RUN cmake --build
RUN make -j 2

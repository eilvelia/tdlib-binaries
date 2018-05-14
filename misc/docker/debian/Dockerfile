FROM debian:9

RUN apt -y update
RUN apt install -y g++ ccache openssl cmake gperf make git libssl-dev libreadline-dev zlib1g zlib1g-dev

RUN openssl version
RUN uname -a

WORKDIR /
RUN git clone https://github.com/tdlib/td.git /td

WORKDIR /td
RUN mkdir build
WORKDIR build
RUN cmake -DCMAKE_BUILD_TYPE=Release ..
#RUN cmake --build .
RUN make -j 2

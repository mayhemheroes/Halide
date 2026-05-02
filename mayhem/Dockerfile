FROM --platform=linux/amd64 ubuntu:22.04 AS builder

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
        wget gnupg ca-certificates lsb-release software-properties-common

RUN wget -qO /tmp/llvm.sh https://apt.llvm.org/llvm.sh && \
    bash /tmp/llvm.sh 21 && \
    rm /tmp/llvm.sh

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
    build-essential cmake libz-dev pkg-config libpng-dev libjpeg-turbo8-dev \
    clang-21 libclang-21-dev liblld-21-dev llvm-21-dev libncurses-dev

RUN update-alternatives --install /usr/bin/llvm-config llvm-config /usr/bin/llvm-config-21 100 && \
    update-alternatives --install /usr/bin/clang clang /usr/bin/clang-21 100 && \
    update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-21 100

COPY . /repo
WORKDIR /repo
RUN make -j8
RUN clang++-21 /repo/mayhem/fuzz_strip_namespaces.cpp -fsanitize=fuzzer,address /repo/lib/libHalide.a -lncurses -o /fuzz

FROM ubuntu:22.04 AS package
COPY --from=builder /repo/bin/build_halide_h /
COPY --from=builder /repo/LICENSE.txt /repo/LICENSE.txt
COPY --from=builder /fuzz /fuzz_strip_namespaces

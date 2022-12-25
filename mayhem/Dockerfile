FROM --platform=linux/amd64 ubuntu:22.04 as builder

RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential cmake llvm-dev libz-dev clang libclang-14-dev liblld-12-dev

COPY . /repo
WORKDIR /repo
RUN make -j8 
RUN clang++ /repo/mayhem/fuzz.cpp -fsanitize=fuzzer,address /repo/lib/libHalide.a -lncurses -o /fuzz

FROM ubuntu:22.04 as package
COPY --from=builder /repo/bin/build_halide_h /
COPY --from=builder /repo/LICENSE.txt /repo/LICENSE.txt
COPY --from=builder /fuzz /fuzz

FROM alpine:3.10.2 AS bob

RUN apk add --no-cache \
	bison \
	build-base \
	ca-certificates \
	cmake \
	curl \
	file \
	flex \
	git \
	ninja

ENV LLVM_VERSION 9.0.0

# get sources for llvm

RUN mkdir -p /tmp/src ; \
	cd /tmp/src ; \
	git clone --branch llvmorg-${LLVM_VERSION} https://github.com/llvm/llvm-project.git

# configure

RUN mkdir -p /tmp/build ; \
	cd /tmp/build ; \
	cmake -G Ninja /tmp/src/llvm-project \
	-DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_INSTALL_PREFIX=/opt/toolchain \
	-DLLVM_TARGETS_TO_BUILD=AArch64;ARM

# build

RUN cd /tmp/build ; \
	ninja

# install

RUN cd /tmp/build;
	ninja install

FROM alpine:3.10.2

COPY --from=bob /opt/toolchain /opt/toolchain



FROM alpine:3.10.2 AS bob

ENV MAKE_THREADS 12

RUN apk add --no-cache \
	bison \
	build-base \
	ca-certificates \
	curl \
	file \
	flex \ 
	git

# set versions

ENV BINUTILS_VERSION 2.32
ENV MPFR_VERSION 4.0.2
ENV MPC_VERSION 1.1.0
ENV GMP_VERSION 6.1.2
ENV GCC_VERSION 9.2.0
ENV LIBC_VERSION 2.0.0


RUN mkdir -p /tmp/src/binutils /tmp/src/gcc/mpc /tmp/src/gcc/mpfr /tmp/src/gcc/gmp /tmp/src/avrlibc /tmp/dl /opt/toolchain
# get sources

RUN curl -L -o /tmp/dl/binutils.tar.bz2 "https://ftpmirror.gnu.org/binutils/binutils-${BINUTILS_VERSION}.tar.bz2" ; \
	curl -L -o /tmp/dl/mpfr.tar.bz2 "http://ftp.funet.fi/pub/gnu/ftp.gnu.org/gnu/mpfr/mpfr-${MPFR_VERSION}.tar.bz2" ; \
	curl -L -o /tmp/dl/mpc.tar.gz "http://ftp.funet.fi/pub/gnu/ftp.gnu.org/gnu/mpc/mpc-${MPC_VERSION}.tar.gz" ; \
	curl -L -o /tmp/dl/gmp.tar.bz2 "http://ftp.funet.fi/pub/gnu/ftp.gnu.org/gnu/gmp/gmp-${GMP_VERSION}.tar.bz2" ; \
	curl -L -o /tmp/dl/gcc.tar.xz "http://ftp.funet.fi/pub/gnu/ftp.gnu.org/gnu/gcc/gcc-${GCC_VERSION}/gcc-${GCC_VERSION}.tar.xz" ; \
	curl -L -o /tmp/dl/avrlibc.tar.bz2 "http://download.savannah.gnu.org/releases/avr-libc/avr-libc-${LIBC_VERSION}.tar.bz2"

# extract

RUN tar xjf /tmp/dl/binutils.tar.bz2 --strip-components=1 -C /tmp/src/binutils ; \
	tar xJf /tmp/dl/gcc.tar.xz --strip-components=1 -C /tmp/src/gcc ; \
	tar xjf /tmp/dl/mpfr.tar.bz2 --strip-components=1 -C /tmp/src/gcc/mpfr ; \
	tar xzf /tmp/dl/mpc.tar.gz --strip-components=1 -C /tmp/src/gcc/mpc ; \
	tar xjf /tmp/dl/gmp.tar.bz2 --strip-components=1 -C /tmp/src/gcc/gmp ; \
	tar xjf /tmp/dl/avrlibc.tar.bz2 --strip-components= -C /tmp/src/avrlibc ; \
# remove downloads
	rm -rf /tmp/dl

# build binutils

RUN mkdir -p /tmp/build/binutils ; \
	cd /tmp/build/binutils ; \
	/tmp/src/binutils/configure --prefix=/opt/toolchain --target=avr --disable-nls ; \
	make -j${MAKE_THREADS} ; \
	make install ; \
	cd / ; \
	rm -rf /tmp/src/binutils ; \
	rm -rf /tmp/build/binutils

# populate PATH

ENV PATH="/opt/toolchain/bin:${PATH}"

# build gcc

RUN mkdir -p /tmp/build/gcc ; \
	cd /tmp/build/gcc ; \
	/tmp/src/gcc/configure --prefix=/opt/toolchain --target=avr --enable-languages=c,c++ --disable-nls --disable-libssp --with-dwarf2 ; \
	make -j${MAKE_THREADS} ; \
	make install ; \
	cd / ; \
	rm -rf /tmp/src/gcc ; \
	rm -rf /tmp/build/gcc

# build avr-libc

RUN mkdir -p /tmp/build/avrlibc ; \
	cd /tmp/build/avrlibc ; \
	/tmp/src/avrlibc/configure --prefix=/opt/toolchain --build=x86_64-alpine-linux-musl --host=avr \
	make -j${MAKE_THREADS} ; \
	make install ; \
	cd / ; \
	rm -rf /tmp/src/avrlibc ; \
	rm -rf /tmp/build/avrlibc

FROM alpine:3.10.2

LABEL maintainer="arto.kitula@gmail.com"
LABEL version="0.9.1"
LABEL description="AVR toolchain"

RUN apk add --no-cache \
	cmake \
	ninja \
	make

ENV PATH="/opt/toolchain/bin:${PATH}"

COPY --from=bob /opt/toolchain /opt/toolchain


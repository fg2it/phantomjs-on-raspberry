FROM resin/aarch64-debian:jessie

ENV OPENSSL_FLAGS='no-idea no-mdc2 no-rc5 no-zlib enable-tlsext no-ssl2 no-ssl3 no-ssl3-method enable-rfc3779 enable-cms'

RUN apt-get update     && \
    apt-get install -y    \
       build-essential git flex bison gperf python ruby git libfontconfig1-dev \
       dpkg-dev binutils gcc g++ libc-dev && \
   echo "deb-src http://deb.debian.org/debian jessie main" >> /etc/apt/sources.list && \
   apt-get update

WORKDIR /tmp

RUN git clone git://github.com/ariya/phantomjs.git && \
    cd phantomjs           && \
    git checkout 2.1.1     && \
    git submodule init     && \
    git submodule update   && \
    apt-get source openssl && \
    apt-get source icu

RUN echo "Recompiling OpenSSL" && \
    cd phantomjs/openssl-1.0.1t && \
    ./Configure --prefix=/usr --openssldir=/etc/ssl --libdir=lib ${OPENSSL_FLAGS} linux-generic64 && \
    make -j 4 depend && make -j 4 && make install_sw

RUN echo "Building the static version of ICU library..." && \
    cd phantomjs/icu-52.1/source && \
    ./configure --prefix=/usr --enable-static --disable-shared && \
    make -j 4 && make install

#since gcc/g++ fails let s try clang ...
RUN curl -LO http://releases.llvm.org/3.8.0/clang+llvm-3.8.0-aarch64-linux-gnu.tar.xz && \
    tar xf clang+llvm-3.8.0-aarch64-linux-gnu.tar.xz

ENV CC=/tmp/clang+llvm-3.8.0-aarch64-linux-gnu/bin/clang
ENV CXX=/tmp/clang+llvm-3.8.0-aarch64-linux-gnu/bin/clang++

RUN echo "Compiling PhantomJS..." && \
    cd phantomjs && \
    python build.py  --confirm --release --qt-config="-no-pkg-config" --git-clean-qtbase --git-clean-qtwebkit

CMD ["/bin/bash"]

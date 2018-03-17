FROM resin/aarch64-debian:stretch

ENV OPENSSL_FLAGS='no-idea no-mdc2 no-rc5 no-zlib enable-tlsext no-ssl2 no-ssl3 no-ssl3-method enable-rfc3779 enable-cms'

RUN echo "deb-src http://httpredir.debian.org/debian stretch main" >> /etc/apt/sources.list && \
    apt-get update     && \
    apt-get install -y    \
       build-essential git flex bison gperf python ruby git libfontconfig1-dev \
       dpkg-dev binutils gcc g++ libc-dev \
       curl

WORKDIR /tmp

RUN git clone git://github.com/ariya/phantomjs.git && \
    cd phantomjs           && \
    git checkout 2.1.1     && \
    git submodule init     && \
    git submodule update   && \
    apt-get source icu

RUN cd phantomjs && \
    curl -L -O http://http.debian.net/debian/pool/main/o/openssl/openssl_1.0.1t-1+deb8u7.dsc           && \
    curl -L -O http://http.debian.net/debian/pool/main/o/openssl/openssl_1.0.1t.orig.tar.gz            && \
    curl -L -O http://http.debian.net/debian/pool/main/o/openssl/openssl_1.0.1t-1+deb8u7.debian.tar.xz && \
    dpkg-source -x openssl_1.0.1t-1+deb8u7.dsc openssl-1.0.1t && \
    cd openssl-1.0.1t && \
    ./Configure --prefix=/usr --openssldir=/etc/ssl --libdir=lib ${OPENSSL_FLAGS} linux-generic64 && \
    make depend && make -j 4 && make install_sw

RUN echo "Building the static version of ICU library..." && \
    cd phantomjs/icu-57.1/source && \
    ./configure --prefix=/usr --enable-static --disable-shared && \
    make -j 4 && make install

RUN echo "Compiling PhantomJS..." && \
    cd phantomjs && \
    python build.py --confirm --release --qt-config="-no-pkg-config" --git-clean-qtbase --git-clean-qtwebkit
#binary is /tmp/phantomjs/bin/phantomjs

CMD ["/bin/bash"]

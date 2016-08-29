FROM resin/armv7hf-debian:wheezy
MAINTAINER fg2it

ARG BUILDOP

ENV OPENSSL_FLAGS='no-idea no-mdc2 no-rc5 no-zlib enable-tlsext no-ssl2 no-ssl3 no-ssl3-method enable-rfc3779 enable-cms'

RUN echo "deb-src http://httpredir.debian.org/debian wheezy main" >> /etc/apt/sources.list && \
    apt-get update     && \
    apt-get install -y    \
       build-essential git flex bison gperf python ruby git libfontconfig1-dev \
       dpkg-dev binutils gcc g++ libc-dev \
       libjpeg8-dev libpng12-dev

WORKDIR /tmp

RUN git clone git://github.com/ariya/phantomjs.git && \
    cd phantomjs           && \
    git checkout 2.1.1     && \
    git submodule init     && \
    git submodule update   && \
    apt-get source openssl && \
    apt-get source icu

RUN echo "Recompiling OpenSSL" && \
    cd phantomjs/openssl-1.0.1e && \
    ./Configure --prefix=/usr --openssldir=/etc/ssl --libdir=lib ${OPENSSL_FLAGS} linux-armv4 && \
    make depend && make && make install

RUN echo "Building the static version of ICU library..." && \
    cd phantomjs/icu-4.8.1.1/source && \
    ./configure --prefix=/usr --enable-static --disable-shared && \
    make && make install


RUN echo "Compiling PhantomJS..." && \
    cd phantomjs && \
    python build.py ${BUILDOP} --confirm --release --qt-config="-no-pkg-config" --git-clean-qtbase --git-clean-qtwebkit

CMD ["/bin/bash"]
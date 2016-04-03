# phantomjs-on-raspberry (pi 2/jessie)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for arm based raspberry pi 2 running raspbian/jessie.

## Binary
Binary build from commit [b483dd6](https://github.com/ariya/phantomjs/tree/b483dd673a1ca589ff10c5f73dfea1e43bfa3225) :

```bash
$ sudo apt-get install build-essential g++ flex bison gperf ruby perl libsqlite3-dev libfontconfig1-dev libicu-dev libfreetype6 libssl-dev libpng-dev libjpeg-dev
$ git clone git://github.com/ariya/phantomjs.git
$ cd phantomjs
$ git checkout b483dd673a1ca589ff10c5f73dfea1e43bfa3225
$ ./build.sh --jobs 2  #takes over 10 hours
````

You might need to increase swap. This can be done by changing `CONF_SWAPSIZE` (in megabytes,
512 was enough for me)
in `/etc/dphys-swapfile` and restarting the daemon to take the change into account:
```bash 
$ sudo service dphys-swapfile stop
$ sudo service dphys-swapfile start
```

The version used for this build was not yet as static as the developpers want it to be
(see this [issue](https://github.com/ariya/phantomjs/issues/12948)). As a consequence, the binary
in this repo have more dependencies than they should. Precisely, `ldd` reports the following :

> linux-vdso.so.1
> libfontconfig.so.1
> libfreetype.so.6
> **libjpeg.so.62**
> **libpng12.so.0**
> **libssl.so.1.0.0**
> **libcrypto.so.1.0.0**
> libz.so.1
> **libicui18n.so.52**
> **libicuuc.so.52**
> **libicudata.so.52**
> libdl.so.2
> librt.so.1
> libpthread.so.0
> libstdc++.so.6
> libm.so.6
> libgcc_s.so.1
> libc.so.6
> /lib/ld-linux-armhf.so.3
> libexpat.so.1

**Bold** libraries are not supposed to be there. If you plan to use this binary,
you may have to install some of them yourself. This should do the job :

```
sudo apt-get install libssl1.0.0 libjpeg62-turbo libpng12-0 libicu52 #libssl provides libcrypto
```

## Package
The package will install `phantomjs` in `/usr/local/bin` and will install the needed dependencies :
> libfontconfig1, libfreetype6, libjpeg62-turbo, libpng12-0, libicu52, libssl1.0.0, zlib1g

It was created with `fpm` :
```bash
$ fpm -s dir -t deb --description "Unofficial PhantomJS Package" --url https://github.com/fg2it/phantomjs-on-raspberry --license BSD -n phantomjs --vendor "" --maintainer fg2it@gmail.com --version 2.0.0 --depends libfontconfig1 --depends libfreetype6 --depends libjpeg62-turbo --depends libpng12-0 --depends libicu52 --depends libssl1.0.0 --depends zlib1g usr/
```

```bash
$ dpkg -I phantomjs_2.0.0_armhf.deb
 new debian package, version 2.0.
 size 15628884 bytes: control archive=453 bytes.
     348 bytes,    11 lines      control
      58 bytes,     1 lines      md5sums
 Package: phantomjs
 Version: 2.0.0
 License: BSD
 Architecture: armhf
 Maintainer: fg2it@gmail.com
 Installed-Size: 36679
 Depends: libfontconfig1, libfreetype6, libjpeg62-turbo, libpng12-0, libicu52, libssl1.0.0, zlib1g
 Section: default
 Priority: extra
 Homepage: https://github.com/fg2it/phantomjs-on-raspberry
 Description: Unofficial PhantomJS Package
```

## License
see Phantomjs [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).
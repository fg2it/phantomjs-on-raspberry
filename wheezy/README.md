# phantomjs-on-raspberry
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for arm based raspberry pi.

PhantomJS doesn't provide packages for arm, so these packages are here
to allow easy install of recent versions.

Packages were build on a raspberry pi 2 running wheezy raspbian (2015-05-05) from the PhantomJS
[source](https://github.com/ariya/phantomjs). Each directory contains a README.md
file that discribes how binaries were build and so, how you can do it yourself
if you are not confortable with unofficial packages. Debian packages were created with
`fpm` and install `phantomjs` in `/usr/bin/`.

Phantomjs [licence](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

## Caveat

Builds are based on v2.0 of PhantomJS which is not yet as static as the developpers want it to be
(see this [issue](https://github.com/ariya/phantomjs/issues/12948)). As a consequence, the binary
in this repo have more dependencies than they should. Precisely :

> linux-vdso.so.1
> **libssl.so.1.0.0**
> **libcrypto.so.1.0.0**
> libfontconfig.so.1
> libfreetype.so.6
> **libjpeg.so.8**
> **libpng12.so.0**
> libz.so.1
> **libicudata.so.48**
> **libicui18n.so.48**
> **libicuuc.so.48**
> libdl.so.2
> librt.so.1
> libpthread.so.0
> libstdc++.so.6
> libm.so.6
> libgcc_s.so.1
> libc.so.6
> /lib/ld-linux-armhf.so.3
> libexpat.so.1

**Bold** libraries are not supposed to be there. They are not listed in the dependency list
of the .deb, so, you may have to install some of them yourself. This should do the job :

```
sudo apt-get install libssl1.0.0 libjpeg8 libpng12-0 libicu48 #libssl provides libcrypto
```

`libicu48` is likely to be 

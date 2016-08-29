# phantomjs-on-raspberry (pi 2/*wheezy and jessie*)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for arm based raspberry pi 2 running raspbian/wheezy or raspbian/jessie.

## Binary
Binary build from commit tag
[v2.1.1](https://github.com/ariya/phantomjs/tree/2.1.1): I mearly adapt the
[docker build process](https://github.com/ariya/phantomjs/blob/2.1.1/deploy/docker-build.sh).
See the `Dockerfile`.

As can be seen from this `Dockerfile`, the base image used is resin/armv7hf-
debian:wheezy. The build was not done on a Pi but in x64-debian-vm running the
docker container. Nothing fancy on the debian-vm, but the resin container comes
with a nice feature: it inclures a `qemu-arm-static` binary. Thus you can run
it on a x64 cpu easily. The details are in a [blog post](https://resin.io/blog
/building-arm-containers-on-any-x86-machine-even-dockerhub/) of resin.

Basically, for us, mere mortal (well, not exactly since you need to have root
power), we just have to do:
```bash
mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc  
echo ':arm:M::\x7fELF\x01\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x28\x00:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/qemu-arm-static:' > /proc/sys/fs/binfmt_misc/register  
```

## Do it yourself
If you want to build Phantomjs yourself, be patient, your up for several hours.
If you plan to use your raspberry pi, you should :
- increase the swap. This can be done by changing `CONF_SWAPSIZE` (in megabytes,
512 was enough for me)
in `/etc/dphys-swapfile` and restarting the daemon to take the change into account:
```bash 
$ sudo service dphys-swapfile stop
$ sudo service dphys-swapfile start
```
- don't use all the core. This allows to limit memory consumption during build process and, so, swapping (which is bad for memory card and compile time). Two should be fine.
```bash
docker build --build-arg BUILDOP='-j 2' -t phjs-build .
```

## PhantomJs Install
### Manual
You should only have to download the binary and install the dependencies:
```bash
sudo apt-get install libfontconfig
curl -o /tmp/phantomjs -sSL https://github.com/fg2it/phantomjs-on-raspberry/releases/download/v2.1.1-wheezy-jessie/phantomjs
sudo mv /tmp/phantomjs /usr/local/bin/phantomjs
```

### Package
```bash
sudo apt-get install libfontconfig
curl -o /tmp/phantomjs_2.1.1_armhf.deb -sSL https://github.com/fg2it/phantomjs-on-raspberry/releases/download/v2.1.1-wheezy-jessie/phantomjs_2.1.1_armhf.deb
sudo dpkg -i /tmp/phantomjs_2.1.1_armhf.deb
```
The package will install `phantomjs` in `/usr/local/bin`.

The package was created with `fpm` :
```bash
fpm -s dir -t deb --description "Unofficial PhantomJS Package" --url https://github.com/fg2it/phantomjs-on-raspberry --license BSD -n phantomjs --vendor "" --maintainer fg2it@gmail.com --version 2.1.1 --depends libfontconfig1 --depends libfreetype6 usr/
```

```bash
$ dpkg -I phantomjs_2.1.1_armhf.deb
 new debian package, version 2.0.
 size 22593558 bytes: control archive=461 bytes.
     288 bytes,    11 lines      control
     129 bytes,     2 lines      md5sums
 Package: phantomjs
 Version: 2.1.1
 License: BSD
 Architecture: armhf
 Maintainer: fg2it@gmail.com
 Installed-Size: 47386
 Depends: libfontconfig1, libfreetype6
 Section: default
 Priority: extra
 Homepage: https://github.com/fg2it/phantomjs-on-raspberry
 Description: Unofficial PhantomJS Package
 ```

## License
see Phantomjs [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

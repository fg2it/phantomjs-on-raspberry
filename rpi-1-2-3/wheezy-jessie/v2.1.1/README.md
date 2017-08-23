# phantomjs-on-raspberry (pi 1b, 2 and 3/*wheezy and jessie*)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for raspberry pi (1b, 2 and 3) running raspbian/wheezy or raspbian/jessie.

## Binary
Binary build from commit tag [v2.1.1](https://github.com/ariya/phantomjs/tree/2.1.1).
```bash
$ ./phantomjs -v
2.1.1
$ file phantomjs
phantomjs: ELF 32-bit LSB executable, ARM, EABI5 version 1 (GNU/Linux), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 2.6.26, BuildID[sha1]=5b45cada7ea3423e57c04caffe681f14b5301000, stripped
$ readelf -A phantomjs
Attribute Section: aeabi
File Attributes
  Tag_CPU_name: "6"
  Tag_CPU_arch: v6
  Tag_ARM_ISA_use: Yes
  Tag_THUMB_ISA_use: Thumb-1
  Tag_FP_arch: VFPv2
  Tag_ABI_PCS_wchar_t: 4
  Tag_ABI_FP_denormal: Needed
  Tag_ABI_FP_exceptions: Needed
  Tag_ABI_FP_number_model: IEEE 754
  Tag_ABI_align_needed: 8-byte
  Tag_ABI_enum_size: int
  Tag_ABI_HardFP_use: SP and DP
  Tag_ABI_VFP_args: VFP registers
  Tag_DIV_use: Not allowed
```

## Do it yourself
I mearly adapt the
[docker build process](https://github.com/ariya/phantomjs/blob/2.1.1/deploy/docker-build.sh).
See the `Dockerfile`.

As can be seen from this `Dockerfile`, the base image used is resin/rpi-raspbian:wheezy. The build was done on a Pi 1b.

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
docker build --build-arg BUILDOP='-j 1' -t phjs-build .
```

## Dependencies
`ldd` reports the following :

> linux-vdso.so.1
> libfontconfig.so.1
> libfreetype.so.6
> libz.so.1
> libdl.so.2
> librt.so.1
> libpthread.so.0
> libstdc++.so.6
> libm.so.6
> libgcc_s.so.1
> libc.so.6
> /lib/ld-linux-armhf.so.3
> libexpat.so.1

This should do the job :

```bash
sudo apt-get install libfontconfig1 libfreetype6 #libssl provides libcrypto
```

## PhantomJs Install
### Manual
You should only have to download the binary and install the dependencies:
```bash
sudo apt-get install libfontconfig1 libfreetype6
curl -o /tmp/phantomjs -sSL https://github.com/fg2it/phantomjs-on-raspberry/releases/download/v2.1.1-wheezy-jessie-armv6/phantomjs
sudo mv /tmp/phantomjs /usr/local/bin/phantomjs
sudo chmod a+x /usr/local/bin/phantomjs
```

### Package
The package will install `phantomjs` in `/usr/local/bin` and depends on :
> libfontconfig1, libfreetype6

```bash
sudo apt-get install libfontconfig1 libfreetype6
curl -o /tmp/phantomjs_2.1.1_armhf.deb -sSL https://github.com/fg2it/phantomjs-on-raspberry/releases/download/v2.1.1-wheezy-jessie-armv6/phantomjs_2.1.1_armhf.deb
sudo dpkg -i /tmp/phantomjs_2.1.1_armhf.deb
```

The package was created with `fpm` :
```bash
fpm -s dir -t deb --description "Unofficial PhantomJS Package" --url https://github.com/fg2it/phantomjs-on-raspberry --license BSD -n phantomjs --vendor "" --maintainer fg2it@gmail.com --version 2.1.1 --depends libfontconfig1 --depends libfreetype6 usr/
```

```bash
$ dpkg -I phantomjs_2.1.1_armhf.deb
new debian package, version 2.0.
 size 23793264 bytes: control archive=458 bytes.
     288 bytes,    11 lines      control              
     129 bytes,     2 lines      md5sums              
 Package: phantomjs
 Version: 2.1.1
 License: BSD
 Architecture: armhf
 Maintainer: fg2it@gmail.com
 Installed-Size: 57778
 Depends: libfontconfig1, libfreetype6
 Section: default
 Priority: extra
 Homepage: https://github.com/fg2it/phantomjs-on-raspberry
 Description: Unofficial PhantomJS Package
 ```

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

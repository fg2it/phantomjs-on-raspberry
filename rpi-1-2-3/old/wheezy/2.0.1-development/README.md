# Phantomjs for raspberry pi (pi 1b, 2 and 3/wheezy)

## Binary
```bash
$ ./phantomjs -v
2.0.1-development
$ file phantomjs
phantomjs: ELF 32-bit LSB executable, ARM, EABI5 version 1 (GNU/Linux), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 2.6.26, BuildID[sha1]=ab36260941dce9256ab76e30155ae801f94d8d9e, not stripped
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
  Tag_ABI_align_preserved: 8-byte, except leaf SP
  Tag_ABI_enum_size: int
  Tag_ABI_HardFP_use: SP and DP
  Tag_ABI_VFP_args: VFP registers
  Tag_CPU_unaligned_access: v6
  Tag_DIV_use: Not allowed
```

## Do it yourself
See the official [instructions](http://phantomjs.org/build.html) to build phantomjs.
Just use `./build.sh --jobs 2` instead of the simple `./build.sh` otherwise you can
expect to run out of memory during the build process.

You might also need to increase swap. This can be done by changing `CONF_SWAPSIZE` (in megabytes,
512 was enough for me)
in `/etc/dphys-swapfile` and restarting the daemon to take the change into account:
```bash
sudo service dphys-swapfile stop
sudo service dphys-swapfile start
```

## Dependencies
The version used for this build was not yet as static as the developpers want it to be
(see this [issue](https://github.com/ariya/phantomjs/issues/12948)). As a consequence, this binary
has more dependencies than it should. Precisely, `ldd` reports the following :

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

```bash
sudo apt-get install libfontconfig1 libfreetype6 libssl1.0.0 libjpeg8 libpng12-0 libicu48 #libssl provides libcrypto
```

## Package
The package will install `phantomjs` in `/usr/local/bin` and depends on :
> libfontconfig1, libfreetype6, libjpeg8, libpng12-0, libicu48, libssl1.0.0, zlib1g

It was created with `fpm` :
```bash
$ fpm -s dir -t deb --description "Unofficial PhantomJS Package" --url https://github.com/fg2it/phantomjs-on-raspberry --license BSD -n phantomjs --vendor "" --maintainer fg2it@gmail.com --version 2.0.1-development --depends libfontconfig1 --depends libfreetype6 --depends libjpeg8 --depends libpng12-0 --depends libicu48 --depends libssl1.0.0 --depends zlib1g -a armhf usr/
```

```bash
$ dpkg -I phantomjs_2.0.1-development_armhf.deb
 new debian package, version 2.0.
 size 15536912 bytes: control archive=498 bytes.
     353 bytes,    11 lines      control
     129 bytes,     2 lines      md5sums
 Package: phantomjs
 Version: 2.0.1-development
 License: BSD
 Architecture: armhf
 Maintainer: fg2it@gmail.com
 Installed-Size: 36498
 Depends: libfontconfig1, libfreetype6, libjpeg8, libpng12-0, libicu48, libssl1.0.0, zlib1g
 Section: default
 Priority: extra
 Homepage: https://github.com/fg2it/phantomjs-on-raspberry
 Description: Unofficial PhantomJS Package
```

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

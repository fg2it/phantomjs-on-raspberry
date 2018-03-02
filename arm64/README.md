# phantomjs on arm64/aarch64 (debian)

For the time being, this is an **experimental build** of phantomjs 2.1.1 on debian/arm64.

## Binary
Binary is available from [release](https://github.com/fg2it/phantomjs-on-raspberry/releases/) section.
```bash
$ ./phantomjs -v
2.1.1
$ file phantomjs
phantomjs: ELF 64-bit LSB shared object, ARM aarch64, version 1 (GNU/Linux), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, for GNU/Linux 3.7.0, BuildID[sha1]=7a4972017ea5ba05c7e093e283c2898ecff31085, stripped
$ readelf -h phantomjs
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 03 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - GNU
  ABI Version:                       0
  Type:                              DYN (Shared object file)
  Machine:                           AArch64
  Version:                           0x1
[...]
```

## Caution
Minimal testing was done on the binary

## Do it yourself
Build is done within a docker container (aarch64/debian:stretch) using qemu (on
a x86-64 a linux vm).

Starting point of this build is the official
[docker build process](https://github.com/ariya/phantomjs/blob/2.1.1/deploy/docker-build.sh)
but with the following changes :
- debian:stretch instead of wheezy (gcc 4.9, default on wheezy, reports error during phantomjs build).
- openssl is not the default version of debian:stretch (1.1.0) as changes in api produce errors during phantomjs build. It is 1.0.1t (debian:jessie source).
- icu is the default version of debian:stretch (icu-57.1).

## Dependencies
ldd reports the following :
```bash
$ ldd ./phantomjs
libz.so.1 => /lib/aarch64-linux-gnu/libz.so.1 (0x0000004004e0d000)
libfontconfig.so.1 => /usr/lib/aarch64-linux-gnu/libfontconfig.so.1 (0x0000004004e35000)
libfreetype.so.6 => /usr/lib/aarch64-linux-gnu/libfreetype.so.6 (0x0000004004e7c000)
libdl.so.2 => /lib/aarch64-linux-gnu/libdl.so.2 (0x0000004004f1d000)
librt.so.1 => /lib/aarch64-linux-gnu/librt.so.1 (0x0000004004f30000)
libpthread.so.0 => /lib/aarch64-linux-gnu/libpthread.so.0 (0x0000004004f49000)
libstdc++.so.6 => /usr/lib/aarch64-linux-gnu/libstdc++.so.6 (0x0000004004f75000)
libm.so.6 => /lib/aarch64-linux-gnu/libm.so.6 (0x0000004005106000)
libgcc_s.so.1 => /lib/aarch64-linux-gnu/libgcc_s.so.1 (0x00000040051b1000)
libc.so.6 => /lib/aarch64-linux-gnu/libc.so.6 (0x00000040051d3000)
/lib/ld-linux-aarch64.so.1 (0x0000004000000000)
libexpat.so.1 => /lib/aarch64-linux-gnu/libexpat.so.1 (0x000000400531d000)
libpng16.so.16 => /usr/lib/aarch64-linux-gnu/libpng16.so.16 (0x0000004005354000)
```

This should do the job :

```bash
sudo apt-get install libfontconfig1 libfreetype6
```

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

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
Minimal testing was done on the binary.

## Do it yourself
Build is done within a docker container using qemu (on
a x86-64 a linux vm).

Starting point of this build is the official
[docker build process](https://github.com/ariya/phantomjs/blob/2.1.1/deploy/docker-build.sh)
but with the following changes :
- debian:stretch instead of wheezy (gcc 4.9, default on wheezy, reports error during phantomjs build).
- openssl is not the default version of debian:stretch (1.1.0) as changes in api produce errors during phantomjs build. It is 1.0.1t (debian:jessie source).
- icu is the default version of debian:stretch (icu-57.1).

### Dockerfile
The `Dockerfile` is (almost) the one used to build [v2.1.1-stretch-arm64][release]
(the released binary was build using `aarch64/debian:stretch` with `qemu-aarch64-static` from [multiarch][multiarch]).

[release]: https://github.com/fg2it/grafana-on-raspberry/releases/tag/v2.1.1-stretch-arm64
[multiarch]: https://github.com/multiarch/qemu-user-static/releases/download/v2.11.0/qemu-aarch64-static

Before building the image, you need to enable arm64 support in
```bash
sudo mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc
sudo sh -c "echo ':arm64:M::\x7fELF\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\xb7:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff:/usr/bin/qemu-aarch64-static:'  > /proc/sys/fs/binfmt_misc/register"
```
> `qemu-aarch64-static` is already in the `resin/aarch64-debian:stretch` base image

After that, the usual
```bash
docker build -t arm64-phjs-builder .
```
A few hours later, the binary will be in `/tmp/phantomjs/bin/phantomjs`.

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

## Caveat
The use of debian:stretch and its gcc/g++ (version 6.3.0 20170516) default
compilers have a drawback : it introduces an hidden dependency to the new
[c++ abi](https://gcc.gnu.org/onlinedocs/libstdc++/manual/abi.html) which is the default since [gcc5](https://wiki.debian.org/GCC5). So, the binary still depends
on `libstdc++.so.6` but uses the new abi which is not is available in the
`libstdc++.so.6` version on jessie.

```bash
$ readelf -V phantomjs
[...]
Version needs section '.gnu.version_r' contains 7 entries:
 Addr: 0x000000000000c7e8  Offset: 0x00c7e8  Link: 6 (.dynstr)
  000000: Version: 1  File: libz.so.1  Cnt: 1
  0x0010:   Name: ZLIB_1.2.0  Flags: none  Version: 13
  0x0020: Version: 1  File: libdl.so.2  Cnt: 1
  0x0030:   Name: GLIBC_2.17  Flags: none  Version: 12
  0x0040: Version: 1  File: libgcc_s.so.1  Cnt: 2
  0x0050:   Name: GCC_3.4  Flags: none  Version: 14
  0x0060:   Name: GCC_3.0  Flags: none  Version: 9
  0x0070: Version: 1  File: libstdc++.so.6  Cnt: 7
  0x0080:   Name: CXXABI_1.3.8  Flags: none  Version: 15
  0x0090:   Name: GLIBCXX_3.4.9  Flags: none  Version: 11
  0x00a0:   Name: GLIBCXX_3.4.11  Flags: none  Version: 10
  0x00b0:   Name: CXXABI_1.3.9  Flags: none  Version: 8
  0x00c0:   Name: CXXABI_1.3  Flags: none  Version: 7
  0x00d0:   Name: GLIBCXX_3.4.21  Flags: none  Version: 6
  0x00e0:   Name: GLIBCXX_3.4  Flags: none  Version: 5
  0x00f0: Version: 1  File: libpthread.so.0  Cnt: 1
  0x0100:   Name: GLIBC_2.17  Flags: none  Version: 4
  0x0110: Version: 1  File: libm.so.6  Cnt: 1
  0x0120:   Name: GLIBC_2.17  Flags: none  Version: 3
  0x0130: Version: 1  File: libc.so.6  Cnt: 1
  0x0140:   Name: GLIBC_2.17  Flags: none  Version: 2
```
So on jessie, you will typically see
```bash
$ cat /etc/debian_version
8.10
$ ./phantomjs -v
./phantomjs: /usr/lib/aarch64-linux-gnu/libstdc++.so.6: version `CXXABI_1.3.9' not found (required by ./phantomjs)
./phantomjs: /usr/lib/aarch64-linux-gnu/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by ./phantomjs)
```
and this is because, as this shows,
```
$ readelf -V /usr/lib/aarch64-linux-gnu/libstdc++.so.6
[...]
Version definition section '.gnu.version_d' contains 32 entries:
  Addr: 0x0000000000045c70  Offset: 0x045c70  Link: 4 (.dynstr)  000000: Rev: 1  Flags: BASE   Index: 1  Cnt: 1  Name: libstdc++.so.6
  0x001c: Rev: 1  Flags: none  Index: 2  Cnt: 1  Name: GLIBCXX_3.4
  0x0038: Rev: 1  Flags: none  Index: 3  Cnt: 2  Name: GLIBCXX_3.4.1
  0x0054: Parent 1: GLIBCXX_3.4
  0x005c: Rev: 1  Flags: none  Index: 4  Cnt: 2  Name: GLIBCXX_3.4.2
  0x0078: Parent 1: GLIBCXX_3.4.1
  0x0080: Rev: 1  Flags: none  Index: 5  Cnt: 2  Name: GLIBCXX_3.4.3
  0x009c: Parent 1: GLIBCXX_3.4.2
  0x00a4: Rev: 1  Flags: none  Index: 6  Cnt: 2  Name: GLIBCXX_3.4.4
  0x00c0: Parent 1: GLIBCXX_3.4.3
  0x00c8: Rev: 1  Flags: none  Index: 7  Cnt: 2  Name: GLIBCXX_3.4.5
  0x00e4: Parent 1: GLIBCXX_3.4.4
  0x00ec: Rev: 1  Flags: none  Index: 8  Cnt: 2  Name: GLIBCXX_3.4.6
  0x0108: Parent 1: GLIBCXX_3.4.5
  0x0110: Rev: 1  Flags: none  Index: 9  Cnt: 2  Name: GLIBCXX_3.4.7
  0x012c: Parent 1: GLIBCXX_3.4.6
  0x0134: Rev: 1  Flags: none  Index: 10  Cnt: 2  Name: GLIBCXX_3.4.8
  0x0150: Parent 1: GLIBCXX_3.4.7
  0x0158: Rev: 1  Flags: none  Index: 11  Cnt: 2  Name: GLIBCXX_3.4.9
  0x0174: Parent 1: GLIBCXX_3.4.8
  0x017c: Rev: 1  Flags: none  Index: 12  Cnt: 2  Name: GLIBCXX_3.4.10
  0x0198: Parent 1: GLIBCXX_3.4.9
  0x01a0: Rev: 1  Flags: none  Index: 13  Cnt: 2  Name: GLIBCXX_3.4.11
  0x01bc: Parent 1: GLIBCXX_3.4.10
  0x01c4: Rev: 1  Flags: none  Index: 14  Cnt: 2  Name: GLIBCXX_3.4.12
  0x01e0: Parent 1: GLIBCXX_3.4.11
  0x01e8: Rev: 1  Flags: none  Index: 15  Cnt: 2  Name: GLIBCXX_3.4.13
  0x0204: Parent 1: GLIBCXX_3.4.12
  0x020c: Rev: 1  Flags: none  Index: 16  Cnt: 2  Name: GLIBCXX_3.4.14
  0x0228: Parent 1: GLIBCXX_3.4.13
  0x0230: Rev: 1  Flags: none  Index: 17  Cnt: 2  Name: GLIBCXX_3.4.15
  0x024c: Parent 1: GLIBCXX_3.4.14
  0x0254: Rev: 1  Flags: none  Index: 18  Cnt: 2  Name: GLIBCXX_3.4.16
  0x0270: Parent 1: GLIBCXX_3.4.15
  0x0278: Rev: 1  Flags: none  Index: 19  Cnt: 2  Name: GLIBCXX_3.4.17
  0x0294: Parent 1: GLIBCXX_3.4.16
  0x029c: Rev: 1  Flags: none  Index: 20  Cnt: 2  Name: GLIBCXX_3.4.18
  0x02b8: Parent 1: GLIBCXX_3.4.17
  0x02c0: Rev: 1  Flags: none  Index: 21  Cnt: 2  Name: GLIBCXX_3.4.19
  0x02dc: Parent 1: GLIBCXX_3.4.18
  0x02e4: Rev: 1  Flags: none  Index: 22  Cnt: 2  Name: GLIBCXX_3.4.20
  0x0300: Parent 1: GLIBCXX_3.4.19
  0x0308: Rev: 1  Flags: none  Index: 23  Cnt: 1  Name: CXXABI_1.3
  0x0324: Rev: 1  Flags: none  Index: 24  Cnt: 2  Name: CXXABI_1.3.1
  0x0340: Parent 1: CXXABI_1.3
  0x0348: Rev: 1  Flags: none  Index: 25  Cnt: 2  Name: CXXABI_1.3.2
  0x0364: Parent 1: CXXABI_1.3.1
  0x036c: Rev: 1  Flags: none  Index: 26  Cnt: 2  Name: CXXABI_1.3.3
  0x0388: Parent 1: CXXABI_1.3.2
  0x0390: Rev: 1  Flags: none  Index: 27  Cnt: 2  Name: CXXABI_1.3.4
  0x03ac: Parent 1: CXXABI_1.3.3
  0x03b4: Rev: 1  Flags: none  Index: 28  Cnt: 2  Name: CXXABI_1.3.5
  0x03d0: Parent 1: CXXABI_1.3.4
  0x03d8: Rev: 1  Flags: none  Index: 29  Cnt: 2  Name: CXXABI_1.3.6
  0x03f4: Parent 1: CXXABI_1.3.5
  0x03fc: Rev: 1  Flags: none  Index: 30  Cnt: 2  Name: CXXABI_1.3.7
  0x0418: Parent 1: CXXABI_1.3.6
  0x0420: Rev: 1  Flags: none  Index: 31  Cnt: 2  Name: CXXABI_1.3.8
  0x043c: Parent 1: CXXABI_1.3.7
  0x0444: Rev: 1  Flags: none  Index: 32  Cnt: 1  Name: CXXABI_TM_1
[...]
```
the required abi is not supported by this version of `libstdc++` (max supported
are cxxabi 1.3.8 and glibcxx 3.4.20).

It is possible to import the right version of `libstdc++.so.6` on jessie
but this doesn't seems reasonable to advise that
```bash
$ cat /etc/debian_version
8.10
$ cd /tmp
#this is libstdc++ from stretch
$ curl -LO http://security.debian.org/debian-security/pool/updates/main/g/gcc-6/libstdc++6_6.3.0-18+deb9u1_arm64.deb
$ dpkg -i --force-all libstdc++6_6.3.0-18+deb9u1_arm64.deb
$ ./phantomjs -v
2.1.1
```

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

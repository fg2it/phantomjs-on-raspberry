# phantomjs on arm64/aarch64 (jessie/stretch)

For the time being, this is an **experimental build** of phantomjs 2.1.1 on debian/arm64 that is expected to work on jessie and stretch.

## Binary
Binary is available from [release](https://github.com/fg2it/phantomjs-on-raspberry/releases/) section.
```bash
$ ./phantomjs -v
2.1.1
$ file phantomjs
phantomjs: ELF 64-bit LSB shared object, ARM aarch64, version 1 (GNU/Linux), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, for GNU/Linux 3.7.0, BuildID[sha1]=766bd709bf1e1729a67e36ddf765495fed3bc195, stripped
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
- debian:jessie instead of wheezy
- clang 3.8 instead of gcc/g++ to build qt
> Trying to build with gcc/g++ creates error during build of qt (unknown relocation)

### Dockerfile
The `Dockerfile` is the one used to build [v2.1.1-jessie-stretch-arm64][release].

[release]: https://github.com/fg2it/phantomjs-on-raspberry/releases/tag/v2.1.1-jessie-stretch-arm64

Before building the image, you need to enable arm64 support in
```bash
sudo mount binfmt_misc -t binfmt_misc /proc/sys/fs/binfmt_misc
sudo sh -c "echo ':arm64:M::\x7fELF\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\xb7:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff:/usr/bin/qemu-aarch64-static:'  > /proc/sys/fs/binfmt_misc/register"
```
> `qemu-aarch64-static` is already in the `resin/aarch64-debian:jessie` base image

After that, the usual
```bash
docker build -t arm64-phjs-builder .
```
A few hours later, the binary will be in `/tmp/phantomjs/bin/phantomjs`.

## Dependencies
ldd reports the following :
```bash
$ ldd ./phantomjs
libz.so.1 => /lib/aarch64-linux-gnu/libz.so.1 (0x0000004000833000)
libfontconfig.so.1 => /usr/lib/aarch64-linux-gnu/libfontconfig.so.1 (0x000000400085c000)
libfreetype.so.6 => /usr/lib/aarch64-linux-gnu/libfreetype.so.6 (0x00000040008a3000)
libdl.so.2 => /lib/aarch64-linux-gnu/libdl.so.2 (0x0000004000941000)
librt.so.1 => /lib/aarch64-linux-gnu/librt.so.1 (0x0000004000954000)
libpthread.so.0 => /lib/aarch64-linux-gnu/libpthread.so.0 (0x000000400096c000)
libstdc++.so.6 => /usr/lib/aarch64-linux-gnu/libstdc++.so.6 (0x0000004000997000)
libm.so.6 => /lib/aarch64-linux-gnu/libm.so.6 (0x0000004000ab0000)
libgcc_s.so.1 => /lib/aarch64-linux-gnu/libgcc_s.so.1 (0x0000004000b51000)
libc.so.6 => /lib/aarch64-linux-gnu/libc.so.6 (0x0000004000b73000)
/lib/ld-linux-aarch64.so.1 (0x0000004000000000)
libexpat.so.1 => /lib/aarch64-linux-gnu/libexpat.so.1 (0x0000004000cc0000)
libpng12.so.0 => /lib/aarch64-linux-gnu/libpng12.so.0 (0x0000004000cf5000)
```

This should do the job :

```bash
sudo apt-get install libfontconfig1 libfreetype6
```

## Additional Notes

A previous build of phantomjs was done on stretch, but it restricts its
portability due to some changes in `libstdc++.so.6` and gcc/g++ defaults.

g++ has options to use old c++ abi (see `-fabi-compat-version`, `-fabi-version`,
and `-D_GLIBCXX_USE_CXX11_ABI=0`) but I was unable to get rid of the new abi
building on stretch.

Since clang has binary available for aarch64, that was the next trial.

```bash
$ readelf -V phantomjs
[...]
Version needs section '.gnu.version_r' contains 7 entries:
 Addr: 0x0000000000408fe0  Offset: 0x008fe0  Link: 6 (.dynstr)
  000000: Version: 1  File: libz.so.1  Cnt: 1
  0x0010:   Name: ZLIB_1.2.0  Flags: none  Version: 11
  0x0020: Version: 1  File: libdl.so.2  Cnt: 1
  0x0030:   Name: GLIBC_2.17  Flags: none  Version: 10
  0x0040: Version: 1  File: libgcc_s.so.1  Cnt: 1
  0x0050:   Name: GCC_3.0  Flags: none  Version: 7
  0x0060: Version: 1  File: libstdc++.so.6  Cnt: 5
  0x0070:   Name: CXXABI_1.3.8  Flags: none  Version: 12
  0x0080:   Name: GLIBCXX_3.4.9  Flags: none  Version: 9
  0x0090:   Name: GLIBCXX_3.4.11  Flags: none  Version: 8
  0x00a0:   Name: CXXABI_1.3  Flags: none  Version: 6
  0x00b0:   Name: GLIBCXX_3.4  Flags: none  Version: 5
  0x00c0: Version: 1  File: libpthread.so.0  Cnt: 1
  0x00d0:   Name: GLIBC_2.17  Flags: none  Version: 4
  0x00e0: Version: 1  File: libm.so.6  Cnt: 1
  0x00f0:   Name: GLIBC_2.17  Flags: none  Version: 3
  0x0100: Version: 1  File: libc.so.6  Cnt: 1
  0x0110:   Name: GLIBC_2.17  Flags: none  Version: 2
```

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

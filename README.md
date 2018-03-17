# phantomjs-on-raspberry (and experimental arm64) [![Release](https://img.shields.io/github/release/fg2it/phantomjs-on-raspberry.svg)](https://github.com/fg2it/phantomjs-on-raspberry/releases/latest)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for raspberry pi.

PhantomJS doesn't provide packages for arm, so these packages are here
to allow easy install of recent versions.

Packages were build from the PhantomJS
[source](https://github.com/ariya/phantomjs). Each directory contains a README.md
file that discribes how binaries were build and so, how you can do it yourself
if you are not confortable with unofficial packages. Debian packages were created with
`fpm`.

All binaries are available in the [release](https://github.com/fg2it/phantomjs-on-raspberry/releases/) section.

## short story
You probably want the last [release](https://github.com/fg2it/phantomjs-on-raspberry/releases/latest).

## `rpi-1-2-3/`
This folder contains notes for binaries that are compatible with *raspberry pi 1 model b, 2 and 3* : they use *armv6* instructions only.

## `rpi-2-3/`
This folder contains notes for binaries that are compatible with *raspberry pi 2 and 3* : they use *armv7* instructions.

## `arm64/`
This folder contains notes for arm64/aarch64 binaries.

## Caveat
### Raspberry Pi
Beside the builds for v2.1.1, the others are based on v2.0 of PhantomJS which was not
as static as the developpers wanted it to be (see this
[issue](https://github.com/ariya/phantomjs/issues/12948)). As a consequence, these
binaries have more dependencies than they should and are thus bound to a specific distro.

For v2.1.1, you should be able to use the binaries on either wheezy or jessie.
### arm64/aarch64
This is an **experimental build** of 2.1.1. It should work at least on arm64 debian stretch.

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

## See
- [fpm](https://github.com/jordansissel/fpm)
- [source](https://github.com/ariya/phantomjs)

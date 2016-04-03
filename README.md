# phantomjs-on-raspberry (raspbian wheezy and jessie)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for arm based raspberry pi 2.

PhantomJS doesn't provide packages for arm, so these packages are here
to allow easy install of recent versions.

Packages were build on a raspberry pi 2 running raspbian from the PhantomJS
[source](https://github.com/ariya/phantomjs). Each directory contains a README.md
file that discribes how binaries were build and so, how you can do it yourself
if you are not confortable with unofficial packages. Debian packages were created with
`fpm`.

## Caveat
Builds are based on v2.0 of PhantomJS which is not yet as static as the developpers want it to be
(see this [issue](https://github.com/ariya/phantomjs/issues/12948)). As a consequence, the binary
in this repo have more dependencies than they should. 

## License
see Phantomjs [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

## See
- [fpm](https://github.com/jordansissel/fpm)
- [source](https://github.com/ariya/phantomjs)
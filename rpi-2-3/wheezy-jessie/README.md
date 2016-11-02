# phantomjs-on-raspberry (pi 2 and 3/*wheezy and jessie*)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for raspberry pi (2 and 3) running raspbian/wheezy or jessie.

Packages were build, using docker and an armhf wheezy based image, from the PhantomJS
[source](https://github.com/ariya/phantomjs).
Each directory contains a README.md
file that discribes how binaries were build and so, how you can do it yourself
if you are not confortable with unofficial packages. Debian packages were created with
`fpm` and install `phantomjs` in `/usr/local/bin/`.

## License
see PhantomJS [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).

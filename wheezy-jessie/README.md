# phantomjs-on-raspberry (pi 2/*wheezy and jessie*)
[Phantomjs](http://phantomjs.org/) *unofficial* binaries for arm based raspberry pi 2 running raspbian/jessie.

Packages were build, using docker and an armhf wheezy based image, from the PhantomJS
[source](https://github.com/ariya/phantomjs).
Each directory contains a README.md
file that discribes how binaries were build and so, how you can do it yourself
if you are not confortable with unofficial packages. Debian packages were created with
`fpm` and install `phantomjs` in `/usr/local/bin/`.

## License
see Phantomjs [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).
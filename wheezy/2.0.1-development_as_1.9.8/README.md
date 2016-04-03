# Phantomjs for raspberry pi (pi 2/wheezy)
This is a nasty trick: the binary provided here for `phantomjs` is `2.0.1-development`
with a dirty patch of the binary such that it responds `1.9.8` as version ...

A few reasons for that:
- grafana uses phantomjs and it uses npm to install it.
- npm wrapper to install phantomjs wants version 1.9.8, so if you install 2.0.1-dev,
npm will try to install version 1.9.8 anyway.
- npm wrapper for phantomjs defaults on i686 to install phantomjs from binaries on non x64 linux platform
(see this [issue](https://github.com/Medium/phantomjs/issues/376)). Install will succeed but you will have
unusable phantomjs for your raspberry pi.
- the workaround is too install phantomsjs 1.9.8 yourself.

I didn't pay enough attention to that and end up building 2.0.1-dev version.
Unfortunately, building phantomjs from sources takes a *long* time and I still
didn't take time to rebuild the correct version.

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

## Caveat
Builds are based on v2.0 of PhantomJS which is not yet as static as the developpers want it to be
(see this [issue](https://github.com/ariya/phantomjs/issues/12948)). As a consequence, the binary
in this repo have more dependencies than they should. Precisely :

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

```
sudo apt-get install libssl1.0.0 libjpeg8 libpng12-0 libicu48 #libssl provides libcrypto
```

## License
see Phantomjs [license](https://github.com/ariya/phantomjs/blob/master/LICENSE.BSD).
---
layout: article
title: Brew update and Postgres error
previousurl: null
nexturl: null
categories: blog
excerpt: "Postgres error handling and reinstallation after brew update && upgrade"
tags:
  - Homebrew
  - brew
  - update
  - upgrade
  - postgres
  - error
image: rainfall-delta_3B43_trmm_2001-2016_mk-z-ts-model@p005
date: '2021-02-03'
modified: '2021-02-03'
comments: true
share: true
---

## Introduction

This post is a summary of the efforts to save my Postgresql installation under Homebrew in Macos Mojave. The origin of the error most probably was a <span class='terminalapp'>brew</span> update that led to an upgrade from postgres 12 to postgres 13. This caused a connection error and I could not get into my postgres database, either via port 5432 or direct access via <span class='terminalapp'>psql</span>.

In the end I had to remove and reinstall the complete Homebrew setup, also forcing me to [reinstall Ruby and Jekyll](https://karttur.github.io/setup-theme-blog/blog/upgrade-ruby-jekyll/), as well as [imagemagick](https://karttur.github.io/setup-theme-blog/blog/install-imagemagick/), [ffmpeg](https://karttur.github.io/setup-theme-blog/blog/ffmpeg-movie/), [inkscape](https://karttur.github.io/setup-theme-blog/blog/inkscape/) and other applications running under <span class='terminalapp'>brew</span>. 


## Port 5432 not responding

The connection error appears both in Python

```
could not connect to server: Connection refused
	Is the server running on host "localhost" (127.0.0.1) and accepting
	TCP/IP connections on port 5432?
```

as well as if you try to run <span class='terminalapp'>psql</span>

```
$ psql
dyld: Library not loaded: /usr/local/opt/openssl/lib/libssl.1.0.0.dylib
  Referenced from: /usr/local/lib/libpq.5.dylib
  Reason: image not found
Abort trap: 6
```

### System checks

The command <span class='terminal'>which postgres</span> will reveal if any instance of postgres is running:

```
$ which postgres
/usr/local/bin/postgres
```

### Restart postgres

The first alternative to try is to restart postgres

<span class='terminal'>$ brew services restart -vvv postgresql</span>

The brew setup of Postgres stops and starts, but the communication with port 5432 still falters.

```
$ brew services restart -vvv postgresql
Stopping `postgresql`... (might take a while)
==> Successfully stopped `postgresql` (label: homebrew.mxcl.postgresql)
==> Generated plist for postgresql:
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
     <key>KeepAlive</key>
     <true/>
     <key>Label</key>
     <string>homebrew.mxcl.postgresql</string>
     <key>ProgramArguments</key>
     <array>
       <string>/usr/local/opt/postgresql/bin/postgres</string>
       <string>-D</string>
       <string>/usr/local/var/postgres</string>
     </array>
     <key>RunAtLoad</key>
     <true/>
     <key>WorkingDirectory</key>
     <string>/usr/local</string>
     <key>StandardOutPath</key>
     <string>/usr/local/var/log/postgres.log</string>
     <key>StandardErrorPath</key>
     <string>/usr/local/var/log/postgres.log</string>
   </dict>
   </plist>


/bin/launchctl enable gui/501/homebrew.mxcl.postgresql
/bin/launchctl bootstrap gui/501 /Users/thomasgumbricht/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
==> Successfully started `postgresql` (label: homebrew.mxcl.postgresql)
```

The same errors reported above persists after the restart.

### Postgres log file

In my <span class='terminalapp'>brew</span> installed version of postgres, the log file is under:

<span class='file'>/usr/local/var/log/postgres.log</span>

Opening it with a text editor, the last lines read:

```
FATAL:  database files are incompatible with server
DETAIL:  The data directory was initialized by PostgreSQL version 12, which is not compatible with this version 13.1.
```

The next step in my efforts to save the postgres database was thus to reinstall an older version of postgre.

### Installing an older version of pstgres

Check ut which version brew supports:

<span class='terminal'>$ brew search postgresql</span>

I then reinstalled version 12 using brew

<span class='terminal'>$ brew install postgresql@12</span>

```
$ brew install postgresql@12
Updating Homebrew...
==> Auto-updated Homebrew!
Updated Homebrew from c538d4d84 to d2430bcf5.
Updated 2 taps (homebrew/core and homebrew/cask).
==> Updated Formulae
Updated 103 formulae.
==> Updated Casks
Updated 26 casks.

==> Downloading https://homebrew.bintray.com/bottles/postgresql%4012-12.5.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/b00c52d0070387e5eb4a2057e634ba637cb71eccc97aff7999cb3a8600cba1fe?response-content-disposition=att
######################################################################## 100.0%
==> Pouring postgresql@12-12.5.mojave.bottle.tar.gz
==> /usr/local/Cellar/postgresql@12/12.5/bin/initdb --locale=C -E UTF-8 /usr/local/var/postgresql@12
==> Caveats
Previous versions of this formula used the same data directory as
the regular PostgreSQL formula. This causes a conflict if you
try to use both at the same time.

In order to avoid this conflict, you should make sure that the
postgresql@12 data directory is located at:
  /usr/local/var/postgresql@12

This formula has created a default database cluster with:
  initdb --locale=C -E UTF-8 /usr/local/var/postgresql@12
For more details, read:
  https://www.postgresql.org/docs/12/app-initdb.html

postgresql@12 is keg-only, which means it was not symlinked into /usr/local,
because this is an alternate version of another formula.

If you need to have postgresql@12 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/postgresql@12/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find postgresql@12 you may need to set:
  export LDFLAGS="-L/usr/local/opt/postgresql@12/lib"
  export CPPFLAGS="-I/usr/local/opt/postgresql@12/include"

For pkg-config to find postgresql@12 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/postgresql@12/lib/pkgconfig"


To have launchd start postgresql@12 now and restart at login:
  brew services start postgresql@12
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres@12 start
==> Summary
🍺  /usr/local/Cellar/postgresql@12/12.5: 3,224 files, 37.6MB
```

So far so good, but the default version has precedence and need to be uninstalled.

### Uninstall the default version

First stop the default version:

<span class='terminal'>$ pg_ctl -D /usr/local/var/postgres stop</span>

```
$ pg_ctl -D /usr/local/var/postgres stop
pg_ctl: PID file "/usr/local/var/postgres/postmaster.pid" does not exist
Is server running?
```

So I just went on to uninstall

<span class='terminal'>$ brew uninstall postgres</span>

### Start the particular (older) postgress version to see if it works

<span class='terminal'>$ pg_ctl -D //usr/local/var/postgres start</span>

```
$ pg_ctl -D /usr/local/var/postgres@12 start
pg_ctl: directory "/usr/local/var/postgres@12" does not exist
```

No, that did not work.

I thus reinstalled postgres@12:

<span class='terminal'>$ brew reinstall postgresql@12</span>

And then starting the postgres server as a default service worked:

<span class='terminal'>$ brew services start postgresql@12</span>

```
$ brew services start postgresql@12
==> Successfully started `postgresql@12` (label: homebrew.mxcl.postgresql@12)
```

With the sequences above I think I got postgres to work again, but with all the content lost.

### psql not working

 But I cannot get into postgres with psql to set up my default users and passwords.

```
$ psql
dyld: Library not loaded: /usr/local/opt/openssl/lib/libssl.1.0.0.dylib
  Referenced from: /usr/local/lib/libpq.5.dylib
  Reason: image not found
Abort trap: 6
```

So I tried:

<span class='terminal'>$ brew install openssl</span>

```
$ brew install openssl
Warning: openssl@1.1 1.1.1i is already installed and up-to-date.
To reinstall 1.1.1i, run:
  brew reinstall openssl@1.1
```

So I tried the reinstall command

```
$ brew reinstall openssl@1.1
==> Downloading https://homebrew.bintray.com/bottles/openssl%401.1-1.1.1i.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/e292d9b7a7ac0068d280a7c5df4e58f408f96d02e65cbf187a24d640db76e971--openssl@1.1-1.1.1i.mojave.bottle.tar.gz
==> Reinstalling openssl@1.1
==> Pouring openssl@1.1-1.1.1i.mojave.bottle.tar.gz
==> Caveats
A CA file has been bootstrapped using certificates from the system
keychain. To add additional certificates, place .pem files in
  /usr/local/etc/openssl@1.1/certs

and run
  /usr/local/opt/openssl@1.1/bin/c_rehash

openssl@1.1 is keg-only, which means it was not symlinked into /usr/local,
because macOS provides LibreSSL.

If you need to have openssl@1.1 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/openssl@1.1/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find openssl@1.1 you may need to set:
  export LDFLAGS="-L/usr/local/opt/openssl@1.1/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl@1.1/include"

For pkg-config to find openssl@1.1 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/openssl@1.1/lib/pkgconfig"

==> Summary
🍺  /usr/local/Cellar/openssl@1.1/1.1.1i: 8,067 files, 18.4MB
==> Upgrading 13 dependents:
armadillo 9.800.3_1 -> 10.2.0, fftw 3.3.8_1 -> 3.3.9, gdal 2.4.2_5 -> 3.2.1_1, libheif 1.10.0 -> 1.11.0, imagemagick 7.0.9-20 -> 7.0.10-61, nghttp2 1.42.0_1 -> 1.43.0, numpy 1.18.1 -> 1.20.0, osgeo/osgeo4mac/osgeo-gdal 3.0.2 -> 3.1.2_2, osgeo/osgeo4mac/osgeo-postgresql 12.0 -> 12.4, poppler 0.84.0 -> 21.02.0, postgresql 13.1, postgis 3.0.0 -> 3.1.1, wxpython 4.0.7.post2 -> 4.1.1
==> Upgrading wxpython 4.0.7.post2 -> 4.1.1
==> Downloading https://homebrew.bintray.com/bottles/numpy-1.20.0.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/e44c19369ec31e109525df0d700c5b7c6306898ae99313b01f986ca1967ba968--numpy-1.20.0.mojave.bottle.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/wxpython-4.1.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/342cee7f2cc885871b3471ef18e6ce026c93687a930fc7c658afbe21101f10ec?response-con
######################################################################## 100.0%
==> Installing dependencies for wxpython: numpy
==> Installing wxpython dependency: numpy
Error: No such file or directory - getcwd
```

Several large question marks arises, including for _postgresql 13.1_, but nothing to do about that right now.

### psql still not working

As neither <span class='terminalall'>psql</span> nor <span class='app'>tableplus</span> could access postgres, reporting errors with openssl/libssl.

```
psql
dyld: Library not loaded: /usr/local/opt/openssl/lib/libssl.1.0.0.dylib
  Referenced from: /usr/local/lib/libpq.5.dylib
  Reason: image not found
Abort trap: 6
```
I force removed the installed openssl version (1.1):

```
$ brew uninstall --ignore-dependencies openssl
Uninstalling /usr/local/Cellar/openssl@1.1/1.1.1i... (8,067 files, 18.4MB)

Warning: The following openssl@1.1 configuration files have not been removed!
If desired, remove them manually with `rm -rf`:
  /usr/local/etc/openssl@1.1
  /usr/local/etc/openssl@1.1/cert.pem
  /usr/local/etc/openssl@1.1/certs
  /usr/local/etc/openssl@1.1/ct_log_list.cnf
  /usr/local/etc/openssl@1.1/ct_log_list.cnf.dist
  /usr/local/etc/openssl@1.1/misc
  /usr/local/etc/openssl@1.1/misc/CA.pl
  /usr/local/etc/openssl@1.1/misc/tsget
  /usr/local/etc/openssl@1.1/misc/tsget.default
  /usr/local/etc/openssl@1.1/misc/tsget.pl
  /usr/local/etc/openssl@1.1/misc/tsget.pl.default
  /usr/local/etc/openssl@1.1/openssl.cnf
  /usr/local/etc/openssl@1.1/openssl.cnf.dist
  /usr/local/etc/openssl@1.1/private

Warning: The following may be openssl@1.1 configuration files and have not been removed!
If desired, remove them manually with `rm -rf`:
  /usr/local/etc/openssl
```

I followed the instructions and removed

<span class='terminal'>$ rm -rf /usr/local/etc/openssl </span>

Then I tried to install version 1.0.0 instead:

<span class='terminal'>$ brew install https://github.com/tebelorg/Tump/releases/download/v1.0.0/openssl.rb</span>

but this route has been depreciated:

```
$ brew install https://github.com/tebelorg/Tump/releases/download/v1.0.0/openssl.rb
Traceback (most recent call last):
`brew extract` or `brew create` and `brew tap-new` to create a formula file in a tap on GitHub instead.: Invalid usage: Non-checksummed download of openssl formula file from an arbitrary URL is unsupported!  (UsageError)
`brew extract` or `brew create` and `brew tap-new` to create a formula file in a tap on GitHub instead.: Invalid usage: Non-checksummed download of openssl formula file from an arbitrary URL is unsupported!  (UsageError)
```

<span class='terminal'>$ brew search openssl</span>

```
$ brew search openssl
Warning: Unexpected method 'license' called on Cask wch-ch34x-usb-serial-driver.
Follow the instructions here:
  https://github.com/Homebrew/homebrew-cask#reporting-bugs
==> Formulae
glib-openssl openssl@1.1 homebrew/portable-ruby/portable-openssl
```

No great success, so I just installed the default again:

<span class='terminal'>$ brew install openssl</span>

```
$ brew install openssl
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
Updated 2 formulae.

==> Downloading https://homebrew.bintray.com/bottles/openssl%401.1-1.1.1i.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/e292d9b7a7ac0068d280a7c5df4e58f408f96d02e65cbf187a24d640db76e971--openssl@1.1-1.1.1i.mojave.bottle.tar.gz
==> Pouring openssl@1.1-1.1.1i.mojave.bottle.tar.gz
==> Caveats
A CA file has been bootstrapped using certificates from the system
keychain. To add additional certificates, place .pem files in
  /usr/local/etc/openssl@1.1/certs

and run
  /usr/local/opt/openssl@1.1/bin/c_rehash

openssl@1.1 is keg-only, which means it was not symlinked into /usr/local,
because macOS provides LibreSSL.

If you need to have openssl@1.1 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/openssl@1.1/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find openssl@1.1 you may need to set:
  export LDFLAGS="-L/usr/local/opt/openssl@1.1/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl@1.1/include"

For pkg-config to find openssl@1.1 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/openssl@1.1/lib/pkgconfig"

==> Summary
🍺  /usr/local/Cellar/openssl@1.1/1.1.1i: 8,067 files, 18.4MB
==> Upgrading 14 dependents:
armadillo 9.800.3_1 -> 10.2.0, curl 7.74.0 -> 7.75.0, fftw 3.3.8_1 -> 3.3.9, libheif 1.10.0 -> 1.11.0, imagemagick 7.0.9-20 -> 7.0.10-61, nghttp2 1.42.0_1 -> 1.43.0, numpy 1.18.1 -> 1.20.0, gdal 2.4.2_5 -> 3.2.1_1, osgeo/osgeo4mac/osgeo-postgresql 12.0 -> 12.4, osgeo/osgeo4mac/osgeo-gdal 3.0.2 -> 3.1.2_2, poppler 0.84.0 -> 21.02.0, postgresql 13.1, postgis 3.0.0 -> 3.1.1, wxpython 4.0.7.post2 -> 4.1.1
==> Upgrading curl 7.74.0 -> 7.75.0
==> Downloading https://homebrew.bintray.com/bottles/nghttp2-1.43.0.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/cbcac00ca57c0c71e148124ed31cf37abcd28f5adc11565fa51f9f277b401a09?response-content-dispo
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/curl-7.75.0.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/ebcf2c049613a0655429872b1bd109b1dd00ba1721e6356cf2aec8b95ba37e47?response-content-dispo
######################################################################## 100.0%
==> Installing dependencies for curl: nghttp2
==> Installing curl dependency: nghttp2
==> Pouring nghttp2-1.43.0.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/nghttp2/1.43.0: 24 files, 2.6MB
==> Installing curl
==> Pouring curl-7.75.0.mojave.bottle.tar.gz
==> Caveats
curl is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have curl first in your PATH, run:
  echo 'export PATH="/usr/local/opt/curl/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find curl you may need to set:
  export LDFLAGS="-L/usr/local/opt/curl/lib"
  export CPPFLAGS="-I/usr/local/opt/curl/include"

For pkg-config to find curl you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/curl/lib/pkgconfig"

==> Summary
🍺  /usr/local/Cellar/curl/7.75.0: 480 files, 3.7MB
Removing: /usr/local/Cellar/curl/7.74.0... (479 files, 3.7MB)
Removing: /Users/thomasgumbricht/Library/Caches/Homebrew/curl--7.74.0.mojave.bottle.2.tar.gz... (1.2MB)
==> Upgrading fftw 3.3.8_1 -> 3.3.9
==> Downloading https://homebrew.bintray.com/bottles/fftw-3.3.9.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/ad960c339268de67c1d6086b0d205336d1f61a86db8fd7528b98e0d8acf98df5?response-content-dispo
######################################################################## 100.0%
==> Pouring fftw-3.3.9.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/fftw/3.3.9: 73 files, 14.2MB
Removing: /usr/local/Cellar/fftw/3.3.8_1... (73 files, 14.7MB)
==> Upgrading libheif 1.10.0 -> 1.11.0
==> Downloading https://homebrew.bintray.com/bottles/libheif-1.11.0.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/3ae3d4575f77c80b65ba04db875293422530d98ec80a61a6076316958fab6688?response-content-dispo
######################################################################## 100.0%
==> Pouring libheif-1.11.0.mojave.bottle.tar.gz
==> /usr/local/opt/shared-mime-info/bin/update-mime-database /usr/local/share/mime
🍺  /usr/local/Cellar/libheif/1.11.0: 25 files, 2.7MB
Removing: /usr/local/Cellar/libheif/1.10.0... (25 files, 7.3MB)
Removing: /Users/thomasgumbricht/Library/Caches/Homebrew/libheif--1.10.0.mojave.bottle.1.tar.gz... (3.0MB)
==> Upgrading imagemagick 7.0.9-20 -> 7.0.10-61
==> Downloading https://homebrew.bintray.com/bottles/liblqr-0.4.2_1.mojave.bottle.1.tar.gz
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/libomp-11.0.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/7313b982b9edd1f1f52c6434f0cc37c5946fd66e758a43c9f02941b08632cd66?response-content-dispo
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/openexr-2.5.4.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/b9f0b513b60da938425fd93805835cfaaa916db9406687a7aa11112ec152a6f7?response-content-dispo
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/imagemagick-7.0.10-61.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/c9c32d414b7e807503b63ad385b0d080b4ee787bcac010007633161d4a28936f?response-content-dispo
######################################################################## 100.0%
==> Installing dependencies for imagemagick: liblqr, libomp and openexr
==> Installing imagemagick dependency: liblqr
==> Pouring liblqr-0.4.2_1.mojave.bottle.1.tar.gz
🍺  /usr/local/Cellar/liblqr/0.4.2_1: 24 files, 129.5KB
==> Installing imagemagick dependency: libomp
==> Pouring libomp-11.0.1.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/libomp/11.0.1: 9 files, 1.4MB
==> Installing imagemagick dependency: openexr
==> Pouring openexr-2.5.4.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/openexr/2.5.4: 152 files, 6.6MB
==> Installing imagemagick
==> Pouring imagemagick-7.0.10-61.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/imagemagick/7.0.10-61: 797 files, 25.2MB
Removing: /usr/local/Cellar/imagemagick/7.0.9-20... (1,480 files, 24.0MB)
==> Upgrading nghttp2 1.43.0 -> 1.43.0
Removing: /usr/local/Cellar/nghttp2/1.42.0_1... (24 files, 2.6MB)
Removing: /Users/thomasgumbricht/Library/Caches/Homebrew/nghttp2--1.42.0_1.mojave.bottle.tar.gz... (959.9KB)
==> Upgrading numpy 1.18.1 -> 1.20.0
==> Downloading https://homebrew.bintray.com/bottles/numpy-1.20.0.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/e44c19369ec31e109525df0d700c5b7c6306898ae99313b01f986ca1967ba968--numpy-1.20.0.mojave.bottle.tar.gz
==> Pouring numpy-1.20.0.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/numpy/1.20.0: 623 files, 18MB
Removing: /usr/local/Cellar/numpy/1.18.1... (483 files, 15.6MB)
Removing: /Users/thomasgumbricht/Library/Caches/Homebrew/numpy--1.19.5.mojave.bottle.tar.gz... (4.3MB)
==> Upgrading gdal 2.4.2_5 -> 3.2.1_1
==> Downloading https://homebrew.bintray.com/bottles/nspr-4.29.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/654784c0f5fe1b716c05930f394d7c4d3a863fbc8591ecbcb3e7fe08469044e9--nspr-4.29.mojave.bottle.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/nss-3.61.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/e179b52aa2a4122f753c3c1fb07b540611d7bfd5f98b98ba5b6b3c0fcdfce9f9--nss-3.61.mojave.bottle.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/poppler-21.02.0.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/694ef0edf0b9ba31b238b8655e1a36c9eb5697782e4adafef6aa04cf99ce8b16--poppler-21.02.0.mojave.bottle.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/unixodbc-2.3.9.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/b85e8b496d4a649b0301c8e29499a74b6ea2333a1b3f9fed5c6852b840099635--unixodbc-2.3.9.mojave.bottle.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/xerces-c-3.2.3.mojave.bottle.1.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/50acaaa5fb7ddafefec152abc22c634de492c7a99b65c25d6e5391ffd7cddc8b--xerces-c-3.2.3.mojave.bottle.1.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/gdal-3.2.1_1.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/9228838745fdae60bb50c681491e077df52c4ca7f52e0e0632fd6542fd38bc74--gdal-3.2.1_1.mojave.bottle.tar.gz
==> Installing dependencies for gdal: nspr, nss, poppler, unixodbc and xerces-c
==> Installing gdal dependency: nspr
==> Pouring nspr-4.29.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/nspr/4.29: 86 files, 1.1MB
==> Installing gdal dependency: nss
==> Pouring nss-3.61.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/nss/3.61: 224 files, 39.9MB
==> Installing gdal dependency: poppler
==> Pouring poppler-21.02.0.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/poppler/21.02.0: 474 files, 26.5MB
==> Installing gdal dependency: unixodbc
==> Pouring unixodbc-2.3.9.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/unixodbc/2.3.9: 46 files, 1.9MB
==> Installing gdal dependency: xerces-c
==> Pouring xerces-c-3.2.3.mojave.bottle.1.tar.gz
🍺  /usr/local/Cellar/xerces-c/3.2.3: 1,684 files, 36.6MB
==> Installing gdal
==> Pouring gdal-3.2.1_1.mojave.bottle.tar.gz
Warning: gdal dependency gcc was built with a different C++ standard
library (libstdc++ from clang). This may cause problems at runtime.
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink bin/gdal-config
Target /usr/local/bin/gdal-config
is a symlink belonging to osgeo-gdal. You can unlink it:
  brew unlink osgeo-gdal

To force the link and overwrite all conflicting files:
  brew link --overwrite gdal

To list all files that would be deleted:
  brew link --overwrite --dry-run gdal

Possible conflicting files are:
/usr/local/bin/gdal-config -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdal-config
/usr/local/bin/gdal_contour -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdal_contour
/usr/local/bin/gdal_grid -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdal_grid
/usr/local/bin/gdal_rasterize -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdal_rasterize
/usr/local/bin/gdal_translate -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdal_translate
/usr/local/bin/gdaladdo -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdaladdo
/usr/local/bin/gdalbuildvrt -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdalbuildvrt
/usr/local/bin/gdaldem -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdaldem
/usr/local/bin/gdalenhance -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdalenhance
/usr/local/bin/gdalinfo -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdalinfo
/usr/local/bin/gdallocationinfo -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdallocationinfo
/usr/local/bin/gdalmanage -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdalmanage
/usr/local/bin/gdalsrsinfo -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdalsrsinfo
/usr/local/bin/gdaltindex -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdaltindex
/usr/local/bin/gdaltransform -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdaltransform
/usr/local/bin/gdalwarp -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gdalwarp
/usr/local/bin/gnmanalyse -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gnmanalyse
/usr/local/bin/gnmmanage -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/gnmmanage
/usr/local/bin/nearblack -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/nearblack
/usr/local/bin/ogr2ogr -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/ogr2ogr
/usr/local/bin/ogrinfo -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/ogrinfo
/usr/local/bin/ogrlineref -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/ogrlineref
/usr/local/bin/ogrtindex -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/ogrtindex
/usr/local/bin/testepsg -> /usr/local/Cellar/osgeo-gdal/3.0.2/bin/testepsg
/usr/local/include/cpl_atomic_ops.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_atomic_ops.h
/usr/local/include/cpl_auto_close.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_auto_close.h
/usr/local/include/cpl_config.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_config.h
/usr/local/include/cpl_config_extras.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_config_extras.h
/usr/local/include/cpl_conv.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_conv.h
/usr/local/include/cpl_csv.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_csv.h
/usr/local/include/cpl_error.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_error.h
/usr/local/include/cpl_hash_set.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_hash_set.h
/usr/local/include/cpl_http.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_http.h
/usr/local/include/cpl_json.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_json.h
/usr/local/include/cpl_list.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_list.h
/usr/local/include/cpl_minixml.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_minixml.h
/usr/local/include/cpl_minizip_ioapi.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_minizip_ioapi.h
/usr/local/include/cpl_minizip_unzip.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_minizip_unzip.h
/usr/local/include/cpl_minizip_zip.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_minizip_zip.h
/usr/local/include/cpl_multiproc.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_multiproc.h
/usr/local/include/cpl_odbc.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_odbc.h
/usr/local/include/cpl_port.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_port.h
/usr/local/include/cpl_progress.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_progress.h
/usr/local/include/cpl_quad_tree.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_quad_tree.h
/usr/local/include/cpl_spawn.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_spawn.h
/usr/local/include/cpl_string.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_string.h
/usr/local/include/cpl_time.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_time.h
/usr/local/include/cpl_virtualmem.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_virtualmem.h
/usr/local/include/cpl_vsi.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_vsi.h
/usr/local/include/cpl_vsi_error.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_vsi_error.h
/usr/local/include/cpl_vsi_virtual.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cpl_vsi_virtual.h
/usr/local/include/cplkeywordparser.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/cplkeywordparser.h
/usr/local/include/gdal.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal.h
/usr/local/include/gdal_alg.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_alg.h
/usr/local/include/gdal_alg_priv.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_alg_priv.h
/usr/local/include/gdal_csv.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_csv.h
/usr/local/include/gdal_frmts.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_frmts.h
/usr/local/include/gdal_mdreader.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_mdreader.h
/usr/local/include/gdal_pam.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_pam.h
/usr/local/include/gdal_priv.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_priv.h
/usr/local/include/gdal_proxy.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_proxy.h
/usr/local/include/gdal_rat.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_rat.h
/usr/local/include/gdal_simplesurf.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_simplesurf.h
/usr/local/include/gdal_utils.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_utils.h
/usr/local/include/gdal_version.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_version.h
/usr/local/include/gdal_vrt.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdal_vrt.h
/usr/local/include/gdalgeorefpamdataset.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdalgeorefpamdataset.h
/usr/local/include/gdalgrid.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdalgrid.h
/usr/local/include/gdalgrid_priv.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdalgrid_priv.h
/usr/local/include/gdaljp2abstractdataset.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdaljp2abstractdataset.h
/usr/local/include/gdaljp2metadata.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdaljp2metadata.h
/usr/local/include/gdalpansharpen.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdalpansharpen.h
/usr/local/include/gdalwarper.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gdalwarper.h
/usr/local/include/gnm.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gnm.h
/usr/local/include/gnm_api.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gnm_api.h
/usr/local/include/gnmgraph.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/gnmgraph.h
/usr/local/include/memdataset.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/memdataset.h
/usr/local/include/ogr_api.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_api.h
/usr/local/include/ogr_core.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_core.h
/usr/local/include/ogr_feature.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_feature.h
/usr/local/include/ogr_featurestyle.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_featurestyle.h
/usr/local/include/ogr_geocoding.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_geocoding.h
/usr/local/include/ogr_geometry.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_geometry.h
/usr/local/include/ogr_p.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_p.h
/usr/local/include/ogr_spatialref.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_spatialref.h
/usr/local/include/ogr_srs_api.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogr_srs_api.h
/usr/local/include/ogrsf_frmts.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/ogrsf_frmts.h
/usr/local/include/rawdataset.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/rawdataset.h
/usr/local/include/vrtdataset.h -> /usr/local/Cellar/osgeo-gdal/3.0.2/include/vrtdataset.h
Error: Could not symlink share/gdal/GDALLogoBW.svg
Target /usr/local/share/gdal/GDALLogoBW.svg
is a symlink belonging to osgeo-gdal. You can unlink it:
  brew unlink osgeo-gdal

To force the link and overwrite all conflicting files:
  brew link --overwrite osgeo-gdal

To list all files that would be deleted:
  brew link --overwrite --dry-run osgeo-gdal
```

That is a lot of information. So I just skipped all of it.

### openssl@1.0

Looking around a bit more, I found another alternative for <span class='terminalapp'>brew</span> installing openssl1.0, and this time at least the installation went through:

<span class='terminal'>$ brew install rbenv/tap/openssl@1.0</span>

```
brew install rbenv/tap/openssl@1.0
==> Tapping rbenv/tap
Cloning into '/usr/local/Homebrew/Library/Taps/rbenv/homebrew-tap'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 5 (delta 0), pack-reused 0
Unpacking objects: 100% (5/5), done.
Tapped 1 formula (29 files, 28.6KB).
==> Installing openssl@1.0 from rbenv/tap
==> Downloading https://www.openssl.org/source/openssl-1.0.2t.tar.gz
######################################################################## 100.0%
==> perl ./Configure --prefix=/usr/local/Cellar/openssl@1.0/1.0.2t --openssldir=/usr/local/etc/openssl no-ssl2 no-ssl3 no-zlib shared enable-cms darwi
==> make depend
==> make
==> make test
==> make install MANDIR=/usr/local/Cellar/openssl@1.0/1.0.2t/share/man MANSUFFIX=ssl
==> Caveats
A CA file has been bootstrapped using certificates from the SystemRoots
keychain. To add additional certificates (e.g. the certificates added in
the System keychain), place .pem files in
  /usr/local/etc/openssl/certs

and run
  /usr/local/opt/openssl@1.0/bin/c_rehash

openssl@1.0 is keg-only, which means it was not symlinked into /usr/local,
because Apple has deprecated use of OpenSSL in favor of its own TLS and crypto libraries.

If you need to have openssl@1.0 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/openssl@1.0/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find openssl@1.0 you may need to set:
  export LDFLAGS="-L/usr/local/opt/openssl@1.0/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl@1.0/include"

For pkg-config to find openssl@1.0 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/openssl@1.0/lib/pkgconfig"

==> Summary
🍺  /usr/local/Cellar/openssl@1.0/1.0.2t: 1,787 files, 12.0MB, built in 5 minutes 55 seconds
```

However, the same error persists when trying to start <span class='terminalapp'>psql</span>.


### Add openssl1.0 to PATH

Add the path to  <span class='file'>openssl@1.0</span> in <span class='file'>.bash_profile</span>

```
PATH=/usr/local/opt/openssl@1.0/bin:$PATH
```

Restarting first the <span class='app'>terminal</span> and then the machine does not change the error (<span class='terminalapp'>psql</span> still looking for openssl1.0)


### Repeat from start

At this stage I restarted the whole installation but this time installing opnessl1.0 before postgres. Thus this sequence of commands:

<span class='terminal'>$ brew uninstall postgres</span>


```
$ brew uninstall postgres
Warning: Treating postgres as a formula. For the cask, use homebrew/cask/postgres
Uninstalling /usr/local/Cellar/postgresql/12.1... (3,217 files, 37.3MB)
```

<span class='terminal'>$ brew update && brew upgrade</span>

```
brew update && brew upgrade
Updated Homebrew from 8eeb83844 to dcf9d9368.
Updated 2 taps (homebrew/core and homebrew/cask).
==> Updated Formulae
aliddns                                              babel
==> Updated Casks
altserver
==> Upgrading 18 outdated packages:
osgeo/osgeo4mac/osgeo-gdal 3.0.2 -> 3.1.2_2
boost 1.72.0 -> 1.75.0_1
libyaml 0.2.2 -> 0.2.5
perl 5.30.1 -> 5.32.1
sfcgal 1.3.7_2 -> 1.3.9_1
protobuf-c 1.3.2_3 -> 1.3.3_3
wxpython 4.0.7.post2 -> 4.1.1
cgal 5.0.1 -> 5.2
postgis 3.0.0 -> 3.1.1
osgeo/osgeo4mac/osgeo-postgresql 12.0 -> 12.4
osgeo/osgeo4mac/osgeo-libgeotiff 1.5.1_1 -> 1.6.0
libidn 1.35 -> 1.36
libzip 1.5.2 -> 1.7.3
jasper 2.0.16_1 -> 2.0.24
armadillo 9.800.3_1 -> 10.2.0
protobuf 3.11.2 -> 3.14.0
automake 1.16.1_1 -> 1.16.3
osgeo/osgeo4mac/osgeo-netcdf 4.7.3 -> 4.7.4
==> Upgrading osgeo/osgeo4mac/osgeo-netcdf 4.7.3 -> 4.7.4
==> Downloading https://bottle.download.osgeo.org/osgeo-netcdf-4.7.4.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring osgeo-netcdf-4.7.4.mojave.bottle.tar.gz
Warning: osgeo/osgeo4mac/osgeo-netcdf dependency gcc was built with a different C++ standard
library (libstdc++ from clang). This may cause problems at runtime.
🍺  /usr/local/Cellar/osgeo-netcdf/4.7.4: 90 files, 5.9MB
Removing: /usr/local/Cellar/osgeo-netcdf/4.7.3... (96 files, 5.8MB)
==> Upgrading boost 1.72.0 -> 1.75.0_1
==> Downloading https://homebrew.bintray.com/bottles/boost-1.75.0_1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/5257405fda53160eaf020dc974cad328d90d698181cd12
######################################################################## 100.0%
==> Pouring boost-1.75.0_1.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/boost/1.75.0_1: 15,058 files, 601.5MB
Removing: /usr/local/Cellar/boost/1.72.0... (14,466 files, 648.5MB)
==> Upgrading libyaml 0.2.2 -> 0.2.5
==> Downloading https://homebrew.bintray.com/bottles/libyaml-0.2.5.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring libyaml-0.2.5.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/libyaml/0.2.5: 10 files, 301.6KB
Removing: /usr/local/Cellar/libyaml/0.2.2... (9 files, 291.6KB)
==> Upgrading perl 5.30.1 -> 5.32.1
==> Downloading https://homebrew.bintray.com/bottles/perl-5.32.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/45de452aa689c422e320ea35fa040a9cc0c6640c2a3b1d
######################################################################## 100.0%
==> Pouring perl-5.32.1.mojave.bottle.tar.gz
==> Caveats
By default non-brewed cpan modules are installed to the Cellar. If you wish
for your modules to persist across updates we recommend using `local::lib`.

You can set that up like this:
  PERL_MM_OPT="INSTALL_BASE=$HOME/perl5" cpan local::lib
  echo 'eval "$(perl -I$HOME/perl5/lib/perl5 -Mlocal::lib=$HOME/perl5)"' >> /Users/thomasgumbricht/.bash_profile
==> Summary
🍺  /usr/local/Cellar/perl/5.32.1: 2,464 files, 63.3MB
Removing: /usr/local/Cellar/perl/5.30.1... (2,442 files, 61.8MB)
==> Upgrading sfcgal 1.3.7_2 -> 1.3.9_1
==> Downloading https://homebrew.bintray.com/bottles/cgal-5.2.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/7163a4ad9b21491d1d9a10a0c6a126696b8647c4094c3c
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/sfcgal-1.3.9_1.mojave.bottle.1.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/8180bd969a152778f3eedd4149c517ea88d5f22efe1955
######################################################################## 100.0%
==> Installing dependencies for sfcgal: cgal
==> Installing sfcgal dependency: cgal
==> Pouring cgal-5.2.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/cgal/5.2: 3,451 files, 36.6MB
==> Installing sfcgal
==> Pouring sfcgal-1.3.9_1.mojave.bottle.1.tar.gz
🍺  /usr/local/Cellar/sfcgal/1.3.9_1: 117 files, 9.5MB
Removing: /usr/local/Cellar/sfcgal/1.3.7_2... (115 files, 10.6MB)
==> Upgrading protobuf-c 1.3.2_3 -> 1.3.3_3
==> Downloading https://homebrew.bintray.com/bottles/protobuf-3.14.0.mojave.bottle.1.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/0070627fe9b8c1818e54480c272cc00fa71bd5bd944b04
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/protobuf-c-1.3.3_3.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Installing dependencies for protobuf-c: protobuf
==> Installing protobuf-c dependency: protobuf
==> Pouring protobuf-3.14.0.mojave.bottle.1.tar.gz
==> Caveats
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/protobuf
==> Summary
🍺  /usr/local/Cellar/protobuf/3.14.0: 211 files, 17.7MB
==> Installing protobuf-c
==> Pouring protobuf-c-1.3.3_3.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/protobuf-c/1.3.3_3: 14 files, 290.4KB
Removing: /usr/local/Cellar/protobuf-c/1.3.2_3... (14 files, 295.0KB)
==> Upgrading wxpython 4.0.7.post2 -> 4.1.1
==> Downloading https://homebrew.bintray.com/bottles/wxpython-4.1.1.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/a69781127ab2365707ba1def88263921efaa8519a1800497266e4ea2c7627725--wxpython-4.1.1.mojave.bottle.tar.gz
==> Pouring wxpython-4.1.1.mojave.bottle.tar.gz
Warning: wxpython dependency gcc was built with a different C++ standard
library (libstdc++ from clang). This may cause problems at runtime.
🍺  /usr/local/Cellar/wxpython/4.1.1: 1,227 files, 60.6MB
Removing: /usr/local/Cellar/wxpython/4.0.7.post2... (1,167 files, 56.5MB)
==> Upgrading cgal 5.2 -> 5.2
Removing: /usr/local/Cellar/cgal/5.0.1... (3,324 files, 34.5MB)
==> Upgrading postgis 3.0.0 -> 3.1.1
==> Downloading https://homebrew.bintray.com/bottles/postgresql-13.1.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/7c9664a27c103ed5d1041cce454ec8aa5a5bd298780c668ce2d9e465783e915a--postgresql-13.1.mojave.bottle.tar.gz
==> Downloading https://homebrew.bintray.com/bottles/postgis-3.1.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/d90ff542d074be8ac87823764911c34ec778277e6ba32d
######################################################################## 100.0%
==> Installing dependencies for postgis: postgresql
==> Installing postgis dependency: postgresql
==> Pouring postgresql-13.1.mojave.bottle.tar.gz
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink bin/clusterdb
Target /usr/local/bin/clusterdb
is a symlink belonging to osgeo-postgresql. You can unlink it:
  brew unlink osgeo-postgresql

To force the link and overwrite all conflicting files:
  brew link --overwrite postgresql

To list all files that would be deleted:
  brew link --overwrite --dry-run postgresql

Possible conflicting files are:
/usr/local/bin/clusterdb -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/clusterdb
/usr/local/bin/createdb -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/createdb
/usr/local/bin/createuser -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/createuser
/usr/local/bin/dropdb -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/dropdb
/usr/local/bin/dropuser -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/dropuser
/usr/local/bin/ecpg -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/ecpg
/usr/local/bin/initdb -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/initdb
/usr/local/bin/oid2name -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/oid2name
/usr/local/bin/pg_archivecleanup -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_archivecleanup
/usr/local/bin/pg_basebackup -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_basebackup
/usr/local/bin/pg_checksums -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_checksums
/usr/local/bin/pg_config -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_config
/usr/local/bin/pg_controldata -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_controldata
/usr/local/bin/pg_ctl -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_ctl
/usr/local/bin/pg_dump -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_dump
/usr/local/bin/pg_dumpall -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_dumpall
/usr/local/bin/pg_isready -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_isready
/usr/local/bin/pg_receivewal -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_receivewal
/usr/local/bin/pg_recvlogical -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_recvlogical
/usr/local/bin/pg_resetwal -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_resetwal
/usr/local/bin/pg_restore -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_restore
/usr/local/bin/pg_rewind -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_rewind
/usr/local/bin/pg_standby -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_standby
/usr/local/bin/pg_test_fsync -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_test_fsync
/usr/local/bin/pg_test_timing -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_test_timing
/usr/local/bin/pg_upgrade -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_upgrade
/usr/local/bin/pg_waldump -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pg_waldump
/usr/local/bin/pgbench -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/pgbench
/usr/local/bin/postgres -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/postgres
/usr/local/bin/postmaster -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/postmaster
/usr/local/bin/psql -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/psql
/usr/local/bin/reindexdb -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/reindexdb
/usr/local/bin/vacuumdb -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/vacuumdb
/usr/local/bin/vacuumlo -> /usr/local/Cellar/osgeo-postgresql/12.0/bin/vacuumlo
/usr/local/include/ecpg_config.h -> /usr/local/Cellar/osgeo-postgresql/12.0/include/ecpg_config.h
/usr/local/include/ecpg_informix.h -> /usr/local/Cellar/osgeo-postgresql/12.0/include/ecpg_informix.h
/usr/local/include/ecpgerrno.h -> /usr/local/Cellar/osgeo-postgresql/12.0/include/ecpgerrno.h
/usr/local/include/ecpglib.h -> /usr/local/Cellar/osgeo-postgresql/12.0/include/ecpglib.h
/usr/local/include/ecpgtype.h -> /usr/local/Cellar/osgeo-postgresql/12.0/include/ecpgtype.h
Error: Could not symlink include/libpq/libpq-fs.h
Target /usr/local/include/libpq/libpq-fs.h
is a symlink belonging to osgeo-postgresql. You can unlink it:
  brew unlink osgeo-postgresql

To force the link and overwrite all conflicting files:
  brew link --overwrite osgeo-postgresql

To list all files that would be deleted:
  brew link --overwrite --dry-run osgeo-postgresql
(base) Thomass-Air:~ thomasgumbricht$
```

Both error warnings relate to postgres:

```
..
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink bin/clusterdb
Target /usr/local/bin/clusterdb
is a symlink belonging to osgeo-postgresql. You can unlink it:
  brew unlink osgeo-postgresql

To force the link and overwrite all conflicting files:
  brew link --overwrite postgresql

To list all files that would be deleted:
  brew link --overwrite --dry-run postgresql

	..

	Error: Could not symlink include/libpq/libpq-fs.h
Target /usr/local/include/libpq/libpq-fs.h
is a symlink belonging to osgeo-postgresql. You can unlink it:
  brew unlink osgeo-postgresql

To force the link and overwrite all conflicting files:
  brew link --overwrite osgeo-postgresql

To list all files that would be deleted:
  brew link --overwrite --dry-run osgeo-postgresql
```

I thus ran the unlink command:

<span class='terminal'>$ brew unlink osgeo-postgresql</span>

```
brew unlink osgeo-postgresql
Unlinking /usr/local/Cellar/osgeo-postgresql/12.0... 725 symlinks removed.
```

And then I did a rerun of

<span class='terminal'>$ brew update && brew upgrade</span>

```
$ brew update && brew upgrade
Updated Homebrew from dcf9d9368 to 04dab8428.
No changes to formulae.
==> Upgrading 9 outdated packages:
osgeo/osgeo4mac/osgeo-gdal 3.0.2 -> 3.1.2_2
postgis 3.0.0 -> 3.1.1
osgeo/osgeo4mac/osgeo-postgresql 12.0 -> 12.4
osgeo/osgeo4mac/osgeo-libgeotiff 1.5.1_1 -> 1.6.0
libidn 1.35 -> 1.36
libzip 1.5.2 -> 1.7.3
jasper 2.0.16_1 -> 2.0.24
armadillo 9.800.3_1 -> 10.2.0
automake 1.16.1_1 -> 1.16.3
==> Upgrading automake 1.16.1_1 -> 1.16.3
==> Downloading https://homebrew.bintray.com/bottles/automake-1.16.3.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/6e25193e573d0e11376322018c9cdf96ddd68ad7e4fe7b
######################################################################## 100.0%
==> Pouring automake-1.16.3.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/automake/1.16.3: 131 files, 3.4MB
Removing: /usr/local/Cellar/automake/1.16.1_1... (131 files, 3.4MB)
==> Upgrading postgis 3.0.0 -> 3.1.1
==> Downloading https://homebrew.bintray.com/bottles/postgis-3.1.1.mojave.bottle.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/2eb8c7cccbdb6e54bd30e738a1fde3ea06676fbc2e0130636e91bd3ee8900290--postgis-3.1.1.mojave.bottle.tar.gz
==> Pouring postgis-3.1.1.mojave.bottle.tar.gz
Warning: postgis dependency gcc was built with a different C++ standard
library (libstdc++ from clang). This may cause problems at runtime.
🍺  /usr/local/Cellar/postgis/3.1.1: 462 files, 29.7MB
Removing: /usr/local/Cellar/postgis/3.0.0... (441 files, 31.3MB)
==> Upgrading osgeo/osgeo4mac/osgeo-postgresql 12.0 -> 12.4
==> Downloading https://bottle.download.osgeo.org/osgeo-postgresql-12.4.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring osgeo-postgresql-12.4.mojave.bottle.tar.gz
==> Caveats

1 - If you need to link "osgeo-postgresql":

      $ brew link osgeo-postgresql --force

    Previously unlink any other version that you have installed.

2 - If you need to init postgresql just execute the following command:

      $ initdb /usr/local/var/postgresql -E utf8 --locale=en_US.UTF-8

    If the file "/usr/local/var/postgresql/PG_VERSION" exists,
    it is because you already created this in postinstall or a previous installation.

3 - Start using:

      $ pg_ctl start -D /usr/local/var/postgresql

4 - Connecting to our new database

      $ psql -h localhost -d postgres

Note:

  - Services doesn't start properly, add to homebrew.mxcl.osgeo-postgresql.plist:

      <key>EnvironmentVariables</key>
      <dict>
        <key>LC_ALL</key>
        <string>en_US.UTF-8</string>
      </dict>

      issue: https://github.com/OSGeo/homebrew-osgeo4mac/issues/1075#issuecomment-490052517

  - Could not bind ipv6 address database system was not properly shut:

      $ sudo lsof -i :5432 (search PID)

      $ kill PID

  - To migrate existing data from a previous major version of PostgreSQL run:

      $ brew postgresql-upgrade-database

  - For more information see our page with documentation:

      https://osgeo.github.io/homebrew-osgeo4mac

To have launchd start osgeo/osgeo4mac/osgeo-postgresql now and restart at login:
  brew services start osgeo/osgeo4mac/osgeo-postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgresql start
==> Summary
🍺  /usr/local/Cellar/osgeo-postgresql/12.4: 2,177 files, 36.2MB
Removing: /usr/local/Cellar/osgeo-postgresql/12.0... (2,151 files, 34.5MB)
==> Upgrading osgeo/osgeo4mac/osgeo-libgeotiff 1.5.1_1 -> 1.6.0
==> Downloading https://bottle.download.osgeo.org/osgeo-libgeotiff-1.6.0.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring osgeo-libgeotiff-1.6.0.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/osgeo-libgeotiff/1.6.0: 34 files, 542.4KB
Removing: /usr/local/Cellar/osgeo-libgeotiff/1.5.1_1... (34 files, 541.4KB)
==> Upgrading libidn 1.35 -> 1.36
==> Downloading https://homebrew.bintray.com/bottles/libidn-1.36.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/61e978964009ca11bb25bb366f47872b306a54bbec468f
######################################################################## 100.0%
==> Pouring libidn-1.36.mojave.bottle.tar.gz
==> Caveats
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/libidn
==> Summary
🍺  /usr/local/Cellar/libidn/1.36: 72 files, 1.5MB
Removing: /usr/local/Cellar/libidn/1.35... (72 files, 1.4MB)
==> Upgrading libzip 1.5.2 -> 1.7.3
==> Downloading https://homebrew.bintray.com/bottles/libzip-1.7.3.mojave.bottle.tar.gz
######################################################################## 100.0%
==> Pouring libzip-1.7.3.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/libzip/1.7.3: 142 files, 609KB
Removing: /usr/local/Cellar/libzip/1.5.2... (134 files, 579.5KB)
==> Upgrading jasper 2.0.16_1 -> 2.0.24
==> Downloading https://homebrew.bintray.com/bottles/jasper-2.0.24.mojave.bottle.1.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/27c44449772445bef1ad62ebc185f7d1dd7efac480f882
######################################################################## 100.0%
==> Pouring jasper-2.0.24.mojave.bottle.1.tar.gz
🍺  /usr/local/Cellar/jasper/2.0.24: 40 files, 857.2KB
Removing: /usr/local/Cellar/jasper/2.0.16_1... (40 files, 1.4MB)
==> Upgrading armadillo 9.800.3_1 -> 10.2.0
==> Downloading https://homebrew.bintray.com/bottles/superlu-5.2.2.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/84070217c8d262573eacc0d5e5b08ac7e19c68574d0cc2
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/armadillo-10.2.0.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/d1a5ea00a11331b68db7d28b60fee70c7138ca4333871c
######################################################################## 100.0%
==> Installing dependencies for armadillo: superlu
==> Installing armadillo dependency: superlu
==> Pouring superlu-5.2.2.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/superlu/5.2.2: 22 files, 779.7KB
==> Installing armadillo
==> Pouring armadillo-10.2.0.mojave.bottle.tar.gz
Warning: armadillo dependency gcc was built with a different C++ standard
library (libstdc++ from clang). This may cause problems at runtime.
🍺  /usr/local/Cellar/armadillo/10.2.0: 601 files, 5.1MB
Removing: /usr/local/Cellar/armadillo/9.800.3_1... (587 files, 4.7MB)
==> Upgrading osgeo/osgeo4mac/osgeo-gdal 3.0.2 -> 3.1.2_2
==> Downloading https://bottle.download.osgeo.org/osgeo-gdal-3.1.2_2.mojave.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring osgeo-gdal-3.1.2_2.mojave.bottle.1.tar.gz
Warning: osgeo/osgeo4mac/osgeo-gdal dependency gcc was built with a different C++ standard
library (libstdc++ from clang). This may cause problems at runtime.
==> Caveats
Plugins for this version of GDAL/OGR, generated by other formulae, should
be symlinked to the following directory:

  /usr/local/lib/gdalplugins/3.1

You may need to set the following enviroment variable:

  export GDAL_DRIVER_PATH=/usr/local/lib/gdalplugins

PYTHON BINDINGS are now built in a separate formula: osgeo-gdal-python
==> Summary
🍺  /usr/local/Cellar/osgeo-gdal/3.1.2_2: 227 files, 58.8MB
Removing: /usr/local/Cellar/osgeo-gdal/3.0.2... (222 files, 53.6MB)
==> No outdated dependents to upgrade!
==> Checking for dependents of upgraded formulae...
==> Reinstalling 1 broken dependent from source:
osgeo/osgeo4mac/osgeo-gdal
Warning: Unexpected method 'license' called on Cask wch-ch34x-usb-serial-driver.
Follow the instructions here:
  https://github.com/Homebrew/homebrew-cask#reporting-bugs
==> Casks with 'auto_updates' or 'version :latest' will not be upgraded; pass `--greedy` to upgrade them.
==> Upgrading 1 outdated package:
inkscape 0.92.2-1 -> 1.0.2
==> Upgrading inkscape
==> Downloading https://media.inkscape.org/dl/resources/file/Inkscape-1.0.2.dmg
######################################################################## 100.0%
==> Backing App 'Inkscape.app' up to '/usr/local/Caskroom/inkscape/0.92.2-1/Inkscape.app'
==> Removing App '/Applications/Inkscape.app'
==> Unlinking Binary '/usr/local/bin/inkscape'
==> Moving App 'Inkscape.app' to '/Applications/Inkscape.app'
==> Linking Binary 'inkscape.wrapper.sh' to '/usr/local/bin/inkscape'
==> Purging files for version 0.92.2-1 of Cask inkscape
🍺  inkscape was successfully upgraded!
==> Caveats
==> osgeo-postgresql

1 - If you need to link "osgeo-postgresql":

      $ brew link osgeo-postgresql --force

    Previously unlink any other version that you have installed.

2 - If you need to init postgresql just execute the following command:

      $ initdb /usr/local/var/postgresql -E utf8 --locale=en_US.UTF-8

    If the file "/usr/local/var/postgresql/PG_VERSION" exists,
    it is because you already created this in postinstall or a previous installation.

3 - Start using:

      $ pg_ctl start -D /usr/local/var/postgresql

4 - Connecting to our new database

      $ psql -h localhost -d postgres

Note:

  - Services doesn't start properly, add to homebrew.mxcl.osgeo-postgresql.plist:

      <key>EnvironmentVariables</key>
      <dict>
        <key>LC_ALL</key>
        <string>en_US.UTF-8</string>
      </dict>

      issue: https://github.com/OSGeo/homebrew-osgeo4mac/issues/1075#issuecomment-490052517

  - Could not bind ipv6 address database system was not properly shut:

      $ sudo lsof -i :5432 (search PID)

      $ kill PID

  - To migrate existing data from a previous major version of PostgreSQL run:

      $ brew postgresql-upgrade-database

  - For more information see our page with documentation:

      https://osgeo.github.io/homebrew-osgeo4mac

To have launchd start osgeo/osgeo4mac/osgeo-postgresql now and restart at login:
  brew services start osgeo/osgeo4mac/osgeo-postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgresql start
==> libidn
Emacs Lisp files have been installed to:
  /usr/local/share/emacs/site-lisp/libidn
==> osgeo-gdal
Plugins for this version of GDAL/OGR, generated by other formulae, should
be symlinked to the following directory:

  /usr/local/lib/gdalplugins/3.1

You may need to set the following enviroment variable:

  export GDAL_DRIVER_PATH=/usr/local/lib/gdalplugins

PYTHON BINDINGS are now built in a separate formula: osgeo-gdal-python
```

This time there seems to be some more promising results. But there is no posgresql version installed (as I unistalled it above). I just ignore that for now and continue by uninstalling openssl:

<span class='terminal'>$ $ brew uninstall openssl</span>

```
t$ brew uninstall openssl
Error: Refusing to uninstall /usr/local/Cellar/openssl@1.1/1.1.1i
because it is required by armadillo, arpack, cairo, curl, ffmpeg, fftw, gdal, geos, glib, gnutls, gobject-introspection, harfbuzz, imagemagick, krb5, ldns, libass, libdap, libevent, libfido2, libheif, liblqr, libpq, librttopo, libspatialite, libssh2, libxml2, mdbtools, nghttp2, numpy, open-mpi, openldap, openssh, osgeo-gdal, osgeo-libspatialite, osgeo-postgresql, poppler, postgis, postgresql, postgresql@12, python@3.9, rtmpdump, shared-mime-info, srt, tcl-tk, unbound and wxpython, which are currently installed.
You can override this and force removal with:
  brew uninstall --ignore-dependencies openssl
```

To get rid of openssl:

<span class='terminal'>$ brew uninstall --ignore-dependencies openssl</span>

### Reinstall openssl1.0

<span class='terminal'>$ brew install rbenv/tap/openssl@1.0</span>

```
$ brew install rbenv/tap/openssl@1.0
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
Updated 2 formulae.

Warning: rbenv/tap/openssl@1.0 1.0.2t is already installed and up-to-date.
To reinstall 1.0.2t, run:
  brew reinstall openssl@1.0
(base) Thomass-Air:~ thomasgumbricht$
```

OK, let us try that then:

<span class='terminal'>$ brew reinstall openssl@1.0</span>

```
$ brew reinstall openssl@1.0
==> Downloading https://www.openssl.org/source/openssl-1.0.2t.tar.gz
Already downloaded: /Users/thomasgumbricht/Library/Caches/Homebrew/downloads/5d2da6a9ed1a20ef9d1b1418d79aa674cf5577b70ca1ce436fdaadfa04f0429c--openssl-1.0.2t.tar.gz
==> Reinstalling rbenv/tap/openssl@1.0
==> perl ./Configure --prefix=/usr/local/Cellar/openssl@1.0/1.0.2t --openssldir=/usr/local/etc/openssl no-ssl2 no-ssl3 no-zlib shared enable-cms darwin64-x86_
==> make depend
==> make
==> make test
==> make install MANDIR=/usr/local/Cellar/openssl@1.0/1.0.2t/share/man MANSUFFIX=ssl
==> Caveats
A CA file has been bootstrapped using certificates from the SystemRoots
keychain. To add additional certificates (e.g. the certificates added in
the System keychain), place .pem files in
  /usr/local/etc/openssl/certs

and run
  /usr/local/opt/openssl@1.0/bin/c_rehash

openssl@1.0 is keg-only, which means it was not symlinked into /usr/local,
because Apple has deprecated use of OpenSSL in favor of its own TLS and crypto libraries.

If you need to have openssl@1.0 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/openssl@1.0/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find openssl@1.0 you may need to set:
  export LDFLAGS="-L/usr/local/opt/openssl@1.0/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl@1.0/include"

For pkg-config to find openssl@1.0 you may need to set:
  export PKG_CONFIG_PATH="/usr/local/opt/openssl@1.0/lib/pkgconfig"

==> Summary
🍺  /usr/local/Cellar/openssl@1.0/1.0.2t: 1,787 files, 12.0MB, built in 5 minutes 34 seconds
```

And then

<span class='terminal'>$ psql</span>

```
$ psql
dyld: Library not loaded: /usr/local/opt/openssl@1.1/lib/libssl.1.1.dylib
  Referenced from: /usr/local/lib/libpq.5.dylib
  Reason: image not found
Abort trap: 6
```

Now I am getting crazy, this time postgres **wants** _libssl.1.1_. I thus ran the following sequence of commands:

<span class='terminal'>$ brew uninstall openssl@1.0</span>

<span class='terminal'>$ rm -rf /usr/local/etc/openssl</span>

<span class='terminal'>$ rm -rf /usr/local/etc/openssl@1.0</span>

<span class='terminal'>$ rm -rf /usr/local/etc/openssl@1.1</span>

<span class='terminal'>$ brew install openssl@1.1</span>

### Reinstalling Homebrew

the whole exercise above ended with a complete crash of the Homebrew installation. I could not <span class='terminalapp'>brew update</span> or <span class='terminalapp'>brew doctor</span>. I thus uninstalled the complete Homebrew installation as instructed on [this page](https://osxdaily.com/2018/08/12/how-uninstall-homebrew-mac/).

<span class='terminal'>$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"</span>

```
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall.sh)"
Warning: This script will remove:
/Users/thomasgumbricht/Library/Caches/Homebrew/
/Users/thomasgumbricht/Library/Logs/Homebrew/
/usr/local/Caskroom/
/usr/local/Cellar/
/usr/local/bin/brew -> /usr/local/bin/brew
Are you sure you want to uninstall Homebrew? This will remove your installed packages! [y/N] y
==> Removing Homebrew installation...
Warning: Failed to delete /usr/local/Caskroom
rm: /usr/local/Caskroom: Permission denied
Warning: Failed to delete /usr/local/Cellar
rm: /usr/local/Cellar: Permission denied
==> Removing empty directories...
Password:
==> /usr/bin/sudo /usr/bin/find /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/var /usr/local/Caskroom /usr/local/Cellar /usr/local/Homebrew /usr/local/Frameworks -name .DS_Store -delete
==> /usr/bin/sudo /usr/bin/find /usr/local/bin /usr/local/etc /usr/local/include /usr/local/lib /usr/local/opt /usr/local/sbin /usr/local/share /usr/local/var /usr/local/Caskroom /usr/local/Cellar /usr/local/Homebrew /usr/local/Frameworks -depth -type d -empty -exec rmdir {} ;
==> Homebrew uninstalled!
The following possible Homebrew files were not deleted:
/usr/local/.com.apple.installer.keep
/usr/local/Frameworks/
/usr/local/Homebrew/
/usr/local/bin/
/usr/local/etc/
/usr/local/include/
/usr/local/lib/
/usr/local/libexec/
/usr/local/opt/
/usr/local/sbin/
/usr/local/share/
/usr/local/texlive/
/usr/local/var/
You may wish to remove them yourself.
```

I removed all of the listed directories. I then reinstalled Homebrew from scratch.

### brew install postgres

Afer installing, updating and upgrading Homebrew I ran

<span class='terminal'>$ brew install postgres</span>.

```
$ brew install postgres
==> Downloading https://homebrew.bintray.com/bottles/icu4c-67.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/b6069459c78f18045ee922ce5cb5b235d4b479597d79c3c298d09e0de3d70794?response-content-disposit
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/openssl%401.1-1.1.1i.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/f5a348793735d449d990693ab687049fb11c08ade0b74c6f7337a56fc0a77908?response-content-disposit
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/krb5-1.18.3.mojave.bottle.1.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/3d09843ed22dfe2ce8c193eb3c6183eee9c278e06f179773930a8017d649d312?response-content-disposit
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/readline-8.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/1ea5a8050482911b319dc3e1436ee03310ba79d75d855d40114ba6067e01b9c5?response-content-disposit
######################################################################## 100.0%
==> Downloading https://homebrew.bintray.com/bottles/postgresql-13.1.mojave.bottle.tar.gz
==> Downloading from https://d29vzk4ow07wi7.cloudfront.net/1826c98f6d117bd040fbb307c1c95dfa2dee6ff8647ec8010e1b79386aa59eb0?response-content-disposit
######################################################################## 100.0%
==> Installing dependencies for postgresql: icu4c, openssl@1.1, krb5 and readline
==> Installing postgresql dependency: icu4c
==> Pouring icu4c-67.1.mojave.bottle.tar.gz
==> Caveats
icu4c is keg-only, which means it was not symlinked into /usr/local,
because macOS provides libicucore.dylib (but nothing else).

If you need to have icu4c first in your PATH, run:
  echo 'export PATH="/usr/local/opt/icu4c/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile
  echo 'export PATH="/usr/local/opt/icu4c/sbin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find icu4c you may need to set:
  export LDFLAGS="-L/usr/local/opt/icu4c/lib"
  export CPPFLAGS="-I/usr/local/opt/icu4c/include"

==> Summary
🍺  /usr/local/Cellar/icu4c/67.1: 258 files, 71MB
==> Installing postgresql dependency: openssl@1.1
==> Pouring openssl@1.1-1.1.1i.mojave.bottle.tar.gz
==> Caveats
A CA file has been bootstrapped using certificates from the system
keychain. To add additional certificates, place .pem files in
  /usr/local/etc/openssl@1.1/certs

and run
  /usr/local/opt/openssl@1.1/bin/c_rehash

openssl@1.1 is keg-only, which means it was not symlinked into /usr/local,
because macOS provides LibreSSL.

If you need to have openssl@1.1 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/openssl@1.1/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find openssl@1.1 you may need to set:
  export LDFLAGS="-L/usr/local/opt/openssl@1.1/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl@1.1/include"

==> Summary
🍺  /usr/local/Cellar/openssl@1.1/1.1.1i: 8,067 files, 18.4MB
==> Installing postgresql dependency: krb5
==> Pouring krb5-1.18.3.mojave.bottle.1.tar.gz
==> Caveats
krb5 is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have krb5 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/krb5/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile
  echo 'export PATH="/usr/local/opt/krb5/sbin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find krb5 you may need to set:
  export LDFLAGS="-L/usr/local/opt/krb5/lib"
  export CPPFLAGS="-I/usr/local/opt/krb5/include"

==> Summary
🍺  /usr/local/Cellar/krb5/1.18.3: 162 files, 3.9MB
==> Installing postgresql dependency: readline
==> Pouring readline-8.1.mojave.bottle.tar.gz
==> Caveats
readline is keg-only, which means it was not symlinked into /usr/local,
because macOS provides BSD libedit.

For compilers to find readline you may need to set:
  export LDFLAGS="-L/usr/local/opt/readline/lib"
  export CPPFLAGS="-I/usr/local/opt/readline/include"

==> Summary
🍺  /usr/local/Cellar/readline/8.1: 48 files, 1.6MB
==> Installing postgresql
==> Pouring postgresql-13.1.mojave.bottle.tar.gz
==> /usr/local/Cellar/postgresql/13.1/bin/initdb --locale=C -E UTF-8 /usr/local/var/postgres
==> Caveats
To migrate existing data from a previous major version of PostgreSQL run:
  brew postgresql-upgrade-database

This formula has created a default database cluster with:
  initdb --locale=C -E UTF-8 /usr/local/var/postgres
For more details, read:
  https://www.postgresql.org/docs/13/app-initdb.html

To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start
==> Summary
🍺  /usr/local/Cellar/postgresql/13.1: 3,217 files, 38.7MB
==> Caveats
==> icu4c
icu4c is keg-only, which means it was not symlinked into /usr/local,
because macOS provides libicucore.dylib (but nothing else).

If you need to have icu4c first in your PATH, run:
  echo 'export PATH="/usr/local/opt/icu4c/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile
  echo 'export PATH="/usr/local/opt/icu4c/sbin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find icu4c you may need to set:
  export LDFLAGS="-L/usr/local/opt/icu4c/lib"
  export CPPFLAGS="-I/usr/local/opt/icu4c/include"

==> openssl@1.1
A CA file has been bootstrapped using certificates from the system
keychain. To add additional certificates, place .pem files in
  /usr/local/etc/openssl@1.1/certs

and run
  /usr/local/opt/openssl@1.1/bin/c_rehash

openssl@1.1 is keg-only, which means it was not symlinked into /usr/local,
because macOS provides LibreSSL.

If you need to have openssl@1.1 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/openssl@1.1/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find openssl@1.1 you may need to set:
  export LDFLAGS="-L/usr/local/opt/openssl@1.1/lib"
  export CPPFLAGS="-I/usr/local/opt/openssl@1.1/include"

==> krb5
krb5 is keg-only, which means it was not symlinked into /usr/local,
because macOS already provides this software and installing another version in
parallel can cause all kinds of trouble.

If you need to have krb5 first in your PATH, run:
  echo 'export PATH="/usr/local/opt/krb5/bin:$PATH"' >> /Users/thomasgumbricht/.bash_profile
  echo 'export PATH="/usr/local/opt/krb5/sbin:$PATH"' >> /Users/thomasgumbricht/.bash_profile

For compilers to find krb5 you may need to set:
  export LDFLAGS="-L/usr/local/opt/krb5/lib"
  export CPPFLAGS="-I/usr/local/opt/krb5/include"

==> readline
readline is keg-only, which means it was not symlinked into /usr/local,
because macOS provides BSD libedit.

For compilers to find readline you may need to set:
  export LDFLAGS="-L/usr/local/opt/readline/lib"
  export CPPFLAGS="-I/usr/local/opt/readline/include"

==> postgresql
To migrate existing data from a previous major version of PostgreSQL run:
  brew postgresql-upgrade-database

This formula has created a default database cluster with:
  initdb --locale=C -E UTF-8 /usr/local/var/postgres
For more details, read:
  https://www.postgresql.org/docs/13/app-initdb.html

To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start
```

### Start postgres

Trying to start postgres

<span class='terminal'>$ pg_ctl -D /usr/local/var/postgres start </span>


```
$ pg_ctl -D /usr/local/var/postgres start
pg_ctl: another server might be running; trying to start server anyway
waiting for server to start....2021-02-03 19:37:25.652 CET [4591] FATAL:  lock file "postmaster.pid" already exists
2021-02-03 19:37:25.652 CET [4591] HINT:  Is another postmaster (PID 4577) running in data directory "/usr/local/var/postgres"?
 stopped waiting
pg_ctl: could not start server
Examine the log output.
(base) Thomass-Air:~ thomasgumbricht$ which postgres
/usr/local/bin/postgres
```

A closer check on the postgress that is running

<span class='terminal'>$ cat /usr/local/var/postgres/postmaster.pid </span>

```
$ cat /usr/local/var/postgres/postmaster.pid
4577
/usr/local/var/postgres
1612377094
5432
/tmp
localhost
 26845683    327680
ready   
```

Reboot the machine and then restart postgres, as per [this page](https://stackoverflow.com/questions/36436120/fatal-error-lock-file-postmaster-pid-already-exists).

<span class='terminal'>$ brew services restart postgresql</span>

And finally postgres is up and running again.

### Pages used

[stackoverlfow - Cannot connect to Postgres server running through brew services](https://stackoverflow.com/questions/39710384/cannot-connect-to-postgres-server-running-through-brew-services)

[stackoverflow - Is the server running on host “localhost” (::1) and accepting TCP/IP connections on port 5432?](https://stackoverflow.com/questions/37307346/is-the-server-running-on-host-localhost-1-and-accepting-tcp-ip-connections)

[stackoverflow - Postgres.app Could not start on port 5432](https://stackoverflow.com/questions/12028037/postgres-app-could-not-start-on-port-5432)

[stackoverflow - dyld: Library not loaded: /usr/local/opt/icu4c/lib/libicui18n.62.dylib error running php after installing node with brew on Mac](https://stackoverflow.com/questions/53828891/dyld-library-not-loaded-usr-local-opt-icu4c-lib-libicui18n-62-dylib-error-run)

[superuser - How do I fix “Library not loaded: libssl.1.0.0.dylib” with PostgreSQL?](https://superuser.com/questions/391701/how-do-i-fix-library-not-loaded-libssl-1-0-0-dylib-with-postgresql)

# ruby-build

ruby-build is an [rbenv](https://github.com/sstephenson/rbenv) plugin
that provides an `rbenv install` command to compile and install
different versions of Ruby on UNIX-like systems.

You can also use ruby-build without rbenv in environments where you
need precise control over Ruby version installation.


## Installation

### Installing as an rbenv plugin (recommended)

Installing ruby-build as an rbenv plugin will give you access to the
`rbenv install` command.

    git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

This will install the latest development version of ruby-build into
the `~/.rbenv/plugins/ruby-build` directory. From that directory, you
can check out a specific release tag. To update ruby-build, run `git
pull` to download the latest changes.

### Installing as a standalone program (advanced)

Installing ruby-build as a standalone program will give you access to
the `ruby-build` command for precise control over Ruby version
installation. If you have rbenv installed, you will also be able to
use the `rbenv install` command.

    git clone git://github.com/sstephenson/ruby-build.git
    cd ruby-build
    ./install.sh

This will install ruby-build into `/usr/local`. If you do not have
write permission to `/usr/local`, you will need to run `sudo
./install.sh` instead. You can install to a different prefix by
setting the `PREFIX` environment variable.

To update ruby-build after it has been installed, run `git pull` in
your cloned copy of the repository, then re-run the install script.

### Installing with Homebrew (for OS X users)

Mac OS X users can install ruby-build with the
[Homebrew](http://mxcl.github.com/homebrew/) package manager. This
will give you access to the `ruby-build` command. If you have rbenv
installed, you will also be able to use the `rbenv install` command.

*This is the recommended method of installation if you installed rbenv
 with Homebrew.*

    brew install ruby-build

Or, if you would like to install the latest development release:

    brew install --HEAD ruby-build


## Usage

### Using `rbenv install` with rbenv

To install a Ruby version for use with rbenv, run `rbenv install` with
the exact name of the version you want to install. For example,

    rbenv install 1.9.3-p327

Ruby versions will be installed into a directory of the same name
under `~/.rbenv/versions`.

To see a list of all available Ruby versions, run `rbenv install --list`.
You may also tab-complete available Ruby
versions if your rbenv installation is properly configured.

### Using `ruby-build` standalone

If you have installed ruby-build as a standalone program, you can use
the `ruby-build` command to compile and install Ruby versions into
specific locations.

Run the `ruby-build` command with the exact name of the version you
want to install and the full path where you want to install it. For
example,

    ruby-build 1.9.3-p327 ~/local/ruby-1.9.3-p327

To see a list of all available Ruby versions, run `ruby-build
--definitions`.

Pass the `-v` or `--verbose` flag to `ruby-build` as the first
argument to see what's happening under the hood.

### A note about Ruby 2.0 and Mac OS X

Ruby 2.0 is incompatible with the version of OpenSSL bundled with Mac
OS X. To work around this issue, install OpenSSL using Homebrew
instead:

    brew install openssl

Then set the `CONFIGURE_OPTS` environment variable when you build
Ruby:

    CONFIGURE_OPTS=--with-openssl-dir=`brew --prefix openssl` rbenv install 2.0.0-preview1

### Custom definitions

Both `rbenv install` and `ruby-build` accept a path to a custom
definition file in place of a version name. Custom definitions let you
develop and install versions of Ruby that are not yet supported by
ruby-build.

See the [ruby-build built-in
definitions](https://github.com/sstephenson/ruby-build/tree/master/share/ruby-build)
as a starting point for custom definition files.

### Special environment variables

You can set certain environment variables to control the build
process.

* `TMPDIR` sets the location where ruby-build stores temporary files.
* `RUBY_BUILD_BUILD_PATH` sets the location in which sources are
  downloaded and built. By default, this is a subdirectory of
  `TMPDIR`.
* `RUBY_BUILD_CACHE_PATH`, if set, specifies a directory to use for
  caching downloaded package files.
* `RUBY_BUILD_MIRROR_URL` overrides the default mirror URL root to one
  of your choosing.
* `RUBY_BUILD_SKIP_MIRROR`, if set, forces ruby-build to download
  packages from their original source URLs instead of using a mirror.
* `CC` sets the path to the C compiler.
* `CONFIGURE_OPTS` lets you pass additional options to `./configure`.
* `MAKE_OPTS` (or `MAKEOPTS`) lets you pass additional options to
  `make`.

### Checksum verification

If you have the `md5`, `openssl`, or `md5sum` tool installed,
ruby-build will automatically verify the MD5 checksum of each
downloaded package before installing it.

Checksums are optional and specified as anchors on the package URL in
each definition. (All bundled definitions include checksums.)

### Package download mirrors

ruby-build will first attempt to download package files from a mirror
hosted on Amazon S3. If a package is not available on the mirror, if
the mirror is down, or if the download is corrupt, ruby-build will
fall back to the official URL specified in the defintion file.

You can point ruby-build to another mirror by specifying the
`RUBY_BUILD_MIRROR_URL` environment variable--useful if you'd like to
run your own local mirror, for example. Package mirror URLs are
constructed by joining this variable with the MD5 checksum of the
package file.

If you don't have an MD5 program installed, ruby-build will skip the
download mirror and use official URLs instead. You can force
ruby-build to bypass the mirror by setting the
`RUBY_BUILD_SKIP_MIRROR` environment variable.

### Package download caching

You can instruct ruby-build to keep a local cache of downloaded
package files by setting the `RUBY_BUILD_CACHE_PATH` environment
variable. When set, package files will be kept in this directory after
the first successful download and reused by subsequent invocations of
`ruby-build` and `rbenv install`.

The `rbenv install` command defaults this path to `~/.rbenv/cache`, so
in most cases you can enable download caching simply by creating that
directory.

### Keeping the build directory after installation

Both `ruby-build` and `rbenv install` accept the `-k` or `--keep`
flag, which tells ruby-build to keep the downloaded source after
installation. This can be useful if you need to use `gdb` and
`memprof` with Ruby.

Source code will be kept in a parallel directory tree
`~/.rbenv/sources` when using `--keep` with the `rbenv install`
command. You should specify the location of the source code with the
`RUBY_BUILD_BUILD_PATH` environment variable when using `--keep` with
`ruby-build`.


## Getting Help

Please see the [ruby-build
wiki](https://github.com/sstephenson/ruby-build/wiki) for solutions to
common problems.

If you can't find an answer on the wiki, open an issue on the [issue
tracker](https://github.com/sstephenson/ruby-build/issues). Be sure to
include the full build log for build failures.


## Version History

#### 20121120
* Added optional package checksum support. When a package URL is
  annotated with an MD5 checksum, ruby-build will use it to verify
  the contents of the downloaded file. Package URLs in all existing
  definitions have been updated with checksum annotations.
* Added an optional package download cache. When the
  `RUBY_BUILD_CACHE_PATH` environment variable is set to a directory
  of your choice, ruby-build will store downloaded packages there and
  reuse them for future installations.
* Added mirror support for faster package downloads. Packages on the
  official ruby-build mirror will be served via S3. You can point
  ruby-build to your own local package mirror by setting the
  `RUBY_BUILD_MIRROR_URL` environment variable.

#### 20121110
* Added a definition for Ruby 1.9.3-p327
* Fetch Ruby 2.0.0.dev and 1.9.3.dev via Git instead of Subversion

#### 20121104
* Added a definition for Ruby 2.0.0-preview1
* Added a definition for Rubinius 2.0.0-rc1

#### 20121022
* Added a definition for JRuby 1.7.0

#### 20121020

* Added a definition for Ruby 1.9.3-p286
* Added a definition for JRuby 1.7.0-rc2
* Added a definition for JRuby 1.7.0-rc1
* Added a definition for JRuby 1.6.8
* Added a definition for JRuby 1.5.6
* Fetch Ruby 2.0.0.dev via Subversion instead of Git
* Allow hooks to be defined for `rbenv-install`

#### 20120815

* Added a definition for MagLev 1.1.0-dev from git.
* Added a definition for Ruby 1.8.7-p370. (For those having trouble
  installing 1.8.7 with newer versions of glibc, please see
  https://github.com/sstephenson/ruby-build/pull/195#issuecomment-7743664.)
* Updated the package URL in the definition for JRuby 1.7.0-preview1.
* Added a definition for JRuby 1.7.0-preview2.
* Updated the Rubinius 2.0.0-dev definition to use the bundled
  RubyGems version instead of installing its own.
* Added an `rbenv uninstall` command for removing installed versions.
* Improved the option parsing for `ruby-build` and `rbenv-install` so
  options may be placed in any order, and short options may be
  combined (e.g. `-kv`).
* Added a `-l`/`--list` option to `rbenv install` to list all
  available definitions.
* Added a `-v`/`--verbose` option to `rbenv install` to invoke
  `ruby-build` in verbose mode.
* Documented the `-k`/`--keep` flag in the command-line help for
  `ruby-build` and `rbenv install`.

#### 20120524

* Added definitions for JRuby 1.6.7.2 and 1.7.0-preview1.
* Removed the definition for JRuby 1.7.0-dev. (In general we do not
  like to remove definitions, but the JRuby team has deleted the
  1.7.0-dev package from their servers -- caveat emptor.)
* Added support for specifying the build location with the
  `RUBY_BUILD_BUILD_PATH` environment variable.
* Added a `-k`/`--keep` flag to `ruby-build` and `rbenv install` for
  keeping the source code around after installation.
* Updated the readme to emphasize installation as an rbenv plugin.

#### 20120423

* Improved error messages when dependencies are missing.
* XCode 4.3+ may be used to build 1.9.3-p125 and later.
* Updated all Ruby 1.9.2 and 1.9.3 definitions to RubyGems 1.8.23.
* Added definitions for REE 1.8.7-2012.02 and 1.8.7-2009.10.
* Added definitions for JRuby 1.6.7.
* Added definitions for Ruby 1.9.2-p318, 1.9.2-p320, and 1.9.3-p194.

#### 20120216

* Added definitions for REE 1.8.7-2011.12 and 1.8.7-2012.01.
* Added definitions for JRuby 1.6.5.1 and 1.6.6.
* Added definitions for Ruby 1.8.7-p358 and 1.9.3-p125.
* Updated the readme with instructions for installing ruby-build as an
  rbenv plugin.

#### 20111230

* Added a definition for MagLev 1.0.0.
* Added support for overriding `make` options with the
  `$MAKEOPTS`/`$MAKE_OPTS` environment variable.
* Removed RubyGems installations from JRuby definitions in favor of
  the bundled RubyGems versions.
* Added a `before_install_package` hook.
* Added definitions for REE 1.8.7-2009.09 and 1.8.7-2010.01.
* Added definitions for Ruby 1.8.6-p383, 1.8.7-p302 and 1.8.7-p357.
* Updated the JRuby 1.7.0-dev snapshot URL.
* Changed the GCC detector to look for `gcc-*` anywhere in the
  `$PATH`, not just `/usr/bin`.

#### 20111030

* Added a Ruby 1.8.7-p334 definition.
* Renamed the 1.9.4-dev definition to 2.0.0-dev to reflect the new
  version numbering scheme.
* ruby-build now automatically displays the last 10 lines of the error
  log, if any, when a build fails.
* Improved the GCC detection routines and added a more helpful error
  message for Xcode 4.2 users.
* JRuby installation no longer requires the install prefix to exist
  first.
* You can now pass `$CONFIGURE_OPTS` to the REE definitions.
* Added a JRuby 1.6.5 definition.
* Added a Ruby 1.9.2-p180 definition.
* Added a Ruby 1.9.3-p0 definition.

#### 20110928

* ruby-build now uses the `--with-gcc` configure flag on OS X Lion.
* Added definitions for REE 1.8.7-2010.02 and 1.8.6-2009.06.
* Modified `rbenv-install` to run `rbenv rehash` after installation.
* Added a Ruby 1.9.3-rc1 definition.
* Updated the JRuby defintions to install the `jruby-launcher` gem.
* Updated the rbx-2.0.0 definition to point to the master branch.
* Added a jruby-1.7.0-dev definition.
* Added a Ruby 1.9.4-dev definition.

#### 20110914

* Added an rbx-2.0.0-dev definition for Rubinius 2.0.0 from git.
* Added support for setting `./configure` options with the
  `CONFIGURE_OPTS` environment variable.
* Added a 1.9.3-dev definition for Ruby 1.9.3 from Git.
* Added support for fetching package sources via Git.
* Added an `rbenv-install` script which provides an `install` command
  for rbenv users.

#### 20110906.1

* Changed the REE definition not to install its default gem
  collection.
* Reverted a poorly-tested change that intended to enable support for
  relative installation paths.

#### 20110906

 * Initial public release.

### License

(The MIT License)

Copyright (c) 2012 Sam Stephenson

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

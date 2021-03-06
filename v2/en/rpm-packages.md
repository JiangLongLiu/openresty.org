<!---
    @title         RPM Packages
--->

The OpenResty official Yum repositories provide the following RPM packages:

# openresty

This is the production build for the core OpenResty server.

This package registers `/usr/bin/openresty`, which is symbolic link to OpenResty's `nginx` executable
file, `/usr/local/openresty/nginx/sbin/nginx`. This `openresty` command should be visible to your `PATH`
environment by default. Always type `openresty` instead of `nginx` when you want to invoke the nginx
executable provided by this package. The `nginx` executable is not visible to your `PATH` environment
by default, however, to avoid any confusions with other NGINX packages and installations in the same
system.

You can also start the default OpenResty server via the command

```
sudo /sbin/service openresty start
```

Other service actions supported are `stop`, `restart`, and `reload`.

The default server prefix is `/usr/local/openresty/`. For your own OpenResty applications, it is highly
recommended to specify your own server prefix and point it to your own application directories, like this:

```
sudo openresty -p /opt/my-fancy-app/
```

Then you have sub-directories like `conf/`, `html/` and `logs/` under the `/opt/my-fancy-app/` directory.
This way, we can avoid polluting the OpenResty installation trees under `/usr/local/openresty/` and allow
multiple different OpenResty applications sharing the same OpenResty server installation. You will need
to draft up a init script for each of your OpenResty application yourself, however. You can use the default
`/etc/init.d/openresty` init script as a template.

This package enables the dtrace static probes in the NGINX core and some NGINX C modules (like `ngx_http_lua_module`),
which can be consumed by dynamic tracing tools like SystemTap.

We use our own builds of OpenSSL (through the `openresty-openssl` package), PCRE, and zlib to ensure these
critical components are up to date and well formed.

# openresty-resty

This package contains the `resty` command-line utility, which is visible to your `PATH` environment (as
`/usr/bin/resty`. To try it out, just type

```console
$ resty -e 'ngx.say("hello")'
hello
```

This package depends on the standard `perl` package and our `openresty` package to work properly.

See the [resty-cli](https://github.com/openresty/resty-cli) project for more details.

# openresty-doc

This package contains the OpenResty documentation tool chain and documentation data. The most useful tool
is the `restydoc` command-line utility, which should be visible to your `PATH` environment by default (as
`/usr/bin/restydoc`.

To try it out:

```bash
restydoc ngx_lua

restydoc -s content_by_lua

restydoc -s table.concat

restydoc -s proxy_pass
```

See the output of the `restydoc -h` command for more details on its usage.

For the best result, please ensure your terminal is using the UTF-8 character encoding and both of your `perl`
and `groff` installations are modern enough. Otherwise those non-ASCII characters may not be displayed
correctly.

# openresty-debug

This is the normal debug build of OpenResty. As compared to the `openresty` package, it has the following
differences:

1. It disables C compiler optimizations in the build.
2. It enables the NGINX debugging log capability.
3. It uses the `openresty-openssl-debug` package instead of `openresty-openssl` for the OpenSSL library.
4. It enables API checks and assertions in the LuaJIT build.

You should never use this package in production. This package is for development only.

# openresty-valgrind

This is a special debug version of OpenResty targeting the Valgrind tool chain. Valgrind is a powerful tool
to check various kinds of memory issues, like memory leaks and memory invalid accesses. To maximize the
possibilities of catching memory bugs via Valgrind, this build does the following in addition to those
done in the `openresty-debug` package:

1. It disables the memory pools used in the NGINX by applying the "[no-pool](https://github.com/openresty/no-pool-nginx)" patch.
2. It enforces LuaJIT to use the system allocator instead of its own.
3. It enables the internal Valgrind co-operations in the LuaJIT build.

# openresty-openssl

This is our own build of the OpenSSL library. In particular, we have disabled the threads support in the build
to save some overhead.

# openresty-openssl-debug

This is the debug build of OpenSSL library. As compared to `openresty-openssl`, it has the following changes:

1. It disables any C compiler optimizations.
2. It relies on the libefence library of the standard `ElectricFence` package to do extra checks.
3. It is Valgrind clean and free of any Valgrind false positives.
4. Assembly code is disabled so we always have perfect C-land backtraces and etc.

# perl-Test-Nginx

This is our [Test::Nginx](https://github.com/openresty/test-nginx) test framework. Read the following book chapter on a complete
introduction to this test scaffold:

https://openresty.gitbooks.io/programming-openresty/content/testing/

# Debuginfo Packages

We provide debuginfo packages for those containing binary components like the `openresty` and `openresty-openssl` packages. Their
debuginfo packages just have the `-debuginfo` suffix in their package names, just like other standard RPM packages.

# Source

The source files used to build these packages can be found in the `openresty-packaging` GitHub repository:

https://github.com/openresty/openresty-packaging/tree/master/rpm/

# See Also

See the [Linux Packages](linux-packages.html) page for more details on our official OpenResty package repositories.

# Nginx over ccon

Run [Nginx][] in a [ccon][] container.

## Configure

Edit [`config.json`](config.json) to:

* Use your own `tls` source (or replace the entries in the [`tls`
  directory](tls) with your own content.
* Use your own path for the hosted content, replaing `"source": "."`.

Edit [`nginx.conf`](root/etc/nginx.conf) as you see fit.  It logs to
syslog on `/dev/log` by default.

## Launch

```console
$ ccon
```

And away you go :).

## Maintenance

The host package was built for amd64 using [Gentoo's
www-servers/nginx][gentoo-nginx].  The `nginx` binary and required
libraries were copied into [`root`](root) using [lddtree-builder][].
You can update the binaries from any distribution using
[lddtree-builder][].

[ccon]: https://github.com/wking/ccon
[gentoo-nginx]: https://packages.gentoo.org/packages/www-servers/nginx
[lddtree-builder]: https://github.com/wking/lddtree-builder
[Nginx]: https://nginx.org/
[Portage]: https://wiki.gentoo.org/wiki/Portage

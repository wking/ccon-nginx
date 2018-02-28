# Nginx over ccon

Run [Nginx][] in a [ccon][] container.

## Configure

Edit [`config.json`](config.json) to:

* Update your `hostID` entries to match `id -u` and `id -g`
  respectively.
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

## Port redirects

If your Nginx is [`listen`][listen]ing at a high port so you don't
need [`CAP_NET_BIND_SERVICE`][capabilities.7], but you want to access
it on the usual port for testing, you can use [iptables][] to redirect
connections from port 443 to port 8000:

```console
# iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8000
```

When you no longer need the redirection, you can delete it:

```console
# iptables -t nat -L PREROUTING --line-numbers
Chain PREROUTING (policy ACCEPT)
num  target     prot opt source      destination
1    DOCKER     all  --  anywhere    anywhere       ADDRTYPE match dst-type LOCAL
2    REDIRECT   tcp  --  anywhere    anywhere       tcp dpt:https redir ports 8000
# iptables -t nat -D PREROUTING 2
```

## Maintenance

The host package was built for amd64 using [Gentoo's
www-servers/nginx][gentoo-nginx].  The `nginx` binary and required
libraries were copied into [`root`](root) using [lddtree-builder][].
You can update the binaries from any distribution using
[lddtree-builder][].

## License

Content unique to this license is distributed under the [MIT
license](LICENSE).  The Nginx and other binaries are distributed under
the terms of their individual packages:

```console
root$ gentoo-info
packages:
  dev-libs/libpcre-8.41-r1
    URI: https://packages.gentoo.org/packages/dev-libs/libpcre
    licenses: BSD
    security: cpe:/a:pcre:pcre (https://nvd.nist.gov/vuln/search/results?adv_search=true&cpe=cpe%3A%2Fa%3Apcre%3Apcre)
  dev-libs/openssl-1.0.2n
    URI: https://packages.gentoo.org/packages/dev-libs/openssl
    licenses: openssl
    security: cpe:/a:openssl:openssl (https://nvd.nist.gov/vuln/search/results?adv_search=true&cpe=cpe%3A%2Fa%3Aopenssl%3Aopenssl)
  sys-libs/glibc-2.25-r9
    URI: https://packages.gentoo.org/packages/sys-libs/glibc
    licenses: LGPL-2.1+ BSD HPND ISC inner-net rc PCRE
    security: cpe:/a:gnu:glibc (https://nvd.nist.gov/vuln/search/results?adv_search=true&cpe=cpe%3A%2Fa%3Agnu%3Aglibc)
  sys-libs/zlib-1.2.11-r1
    URI: https://packages.gentoo.org/packages/sys-libs/zlib
    licenses: ZLIB
    security: cpe:/a:gnu:zlib (https://nvd.nist.gov/vuln/search/results?adv_search=true&cpe=cpe%3A%2Fa%3Agnu%3Azlib)
  www-servers/nginx-1.12.2-r1
    URI: https://packages.gentoo.org/packages/www-servers/nginx
    licenses: BSD-2 BSD SSLeay MIT GPL-2 GPL-2+
```

### glibc

Glibc is distributed under the [LGPL-2.1][]+, with some additional
notices from [`LICENSES`][glibc-licenses]:

> ```
> This file contains the copying permission notices for various files in the
> GNU C Library distribution that have copyright owners other than the Free
> Software Foundation.  These notices all require that a copy of the notice
> be included in the accompanying documentation and be distributed with
> binary distributions of the code, so be sure to include this file along
> with any binary distributions derived from the GNU C Library.
>
> All code incorporated from 4.4 BSD is distributed under the following
> license:
>
> Copyright (C) 1991 Regents of the University of California.
> All rights reserved.
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions
> are met:
>
> 1. Redistributions of source code must retain the above copyright
>    notice, this list of conditions and the following disclaimer.
> 2. Redistributions in binary form must reproduce the above copyright
>    notice, this list of conditions and the following disclaimer in the
>    documentation and/or other materials provided with the distribution.
> 3. [This condition was removed.]
> 4. Neither the name of the University nor the names of its contributors
>    may be used to endorse or promote products derived from this software
>    without specific prior written permission.
>
> THIS SOFTWARE IS PROVIDED BY THE REGENTS AND CONTRIBUTORS ``AS IS'' AND
> ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
> IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
> ARE DISCLAIMED.  IN NO EVENT SHALL THE REGENTS OR CONTRIBUTORS BE LIABLE
> FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
> DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
> OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
> HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
> LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
> OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
> SUCH DAMAGE.
>
> The DNS resolver code, taken from BIND 4.9.5, is copyrighted by UC
> Berkeley, by Digital Equipment Corporation and by Internet Software
> Consortium.  The DEC portions are under the following license:
>
> Portions Copyright (C) 1993 by Digital Equipment Corporation.
>
> Permission to use, copy, modify, and distribute this software for any
> purpose with or without fee is hereby granted, provided that the above
> copyright notice and this permission notice appear in all copies, and
> that the name of Digital Equipment Corporation not be used in
> advertising or publicity pertaining to distribution of the document or
> software without specific, written prior permission.
>
> THE SOFTWARE IS PROVIDED ``AS IS'' AND DIGITAL EQUIPMENT CORP.
> DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE, INCLUDING ALL
> IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS.  IN NO EVENT SHALL
> DIGITAL EQUIPMENT CORPORATION BE LIABLE FOR ANY SPECIAL, DIRECT,
> INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING
> FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT,
> NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION
> WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
>
> The ISC portions are under the following license:
>
> Portions Copyright (c) 1996-1999 by Internet Software Consortium.
>
> Permission to use, copy, modify, and distribute this software for any
> purpose with or without fee is hereby granted, provided that the above
> copyright notice and this permission notice appear in all copies.
>
> THE SOFTWARE IS PROVIDED "AS IS" AND INTERNET SOFTWARE CONSORTIUM DISCLAIMS
> ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES
> OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL INTERNET SOFTWARE
> CONSORTIUM BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL
> DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR
> PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS
> ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS
> SOFTWARE.
>
> The Sun RPC support (from rpcsrc-4.0) is covered by the following
> license:
>
> Copyright (c) 2010, Oracle America, Inc.
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions are
> met:
>
> * Redistributions of source code must retain the above copyright
>   notice, this list of conditions and the following disclaimer.
> * Redistributions in binary form must reproduce the above
>   copyright notice, this list of conditions and the following
>   disclaimer in the documentation and/or other materials
>   provided with the distribution.
> * Neither the name of the "Oracle America, Inc." nor the names of its
>   contributors may be used to endorse or promote products derived
>   from this software without specific prior written permission.
>
> THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
> "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
> LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
> FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
> COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT,
> INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
> DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
> GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
> INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
> WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
> NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
> OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
>
> The following CMU license covers some of the support code for Mach,
> derived from Mach 3.0:
>
> Mach Operating System
> Copyright (C) 1991,1990,1989 Carnegie Mellon University
> All Rights Reserved.
>
> Permission to use, copy, modify and distribute this software and its
> documentation is hereby granted, provided that both the copyright
> notice and this permission notice appear in all copies of the
> software, derivative works or modified versions, and any portions
> thereof, and that both notices appear in supporting documentation.
>
> CARNEGIE MELLON ALLOWS FREE USE OF THIS SOFTWARE IN ITS ``AS IS''
> CONDITION.  CARNEGIE MELLON DISCLAIMS ANY LIABILITY OF ANY KIND FOR
> ANY DAMAGES WHATSOEVER RESULTING FROM THE USE OF THIS SOFTWARE.
>
> Carnegie Mellon requests users of this software to return to
>
>  Software Distribution Coordinator
>  School of Computer Science
>  Carnegie Mellon University
>  Pittsburgh PA 15213-3890
>
> or Software.Distribution@CS.CMU.EDU any improvements or
> extensions that they make and grant Carnegie Mellon the rights to
> redistribute these changes.
>
> The file if_ppp.h is under the following CMU license:
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions
> are met:
>
> 1. Redistributions of source code must retain the above copyright
>    notice, this list of conditions and the following disclaimer.
> 2. Redistributions in binary form must reproduce the above copyright
>    notice, this list of conditions and the following disclaimer in the
>    documentation and/or other materials provided with the distribution.
> 3. Neither the name of the University nor the names of its contributors
>    may be used to endorse or promote products derived from this software
>    without specific prior written permission.
>
> THIS SOFTWARE IS PROVIDED BY CARNEGIE MELLON UNIVERSITY AND
> CONTRIBUTORS ``AS IS'' AND ANY EXPRESS OR IMPLIED WARRANTIES,
> INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
> MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED.
> IN NO EVENT SHALL THE UNIVERSITY OR CONTRIBUTORS BE LIABLE FOR ANY
> DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
> DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
> GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
> INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER
> IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
> OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN
> IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
>
> The following license covers the files from Intel's "Highly Optimized
> Mathematical Functions for Itanium" collection:
>
> Intel License Agreement
>
> Copyright (c) 2000, Intel Corporation
>
> All rights reserved.
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions are
> met:
>
> * Redistributions of source code must retain the above copyright
> notice, this list of conditions and the following disclaimer.
>
> * Redistributions in binary form must reproduce the above copyright
> notice, this list of conditions and the following disclaimer in the
> documentation and/or other materials provided with the distribution.
>
> * The name of Intel Corporation may not be used to endorse or promote
> products derived from this software without specific prior written
> permission.
>
> THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
> "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
> LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
> A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL INTEL OR
> CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
> EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
> PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
> PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
> LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
> NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
> SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
>
> The files inet/getnameinfo.c and sysdeps/posix/getaddrinfo.c are copyright
> (C) by Craig Metz and are distributed under the following license:
>
> /* The Inner Net License, Version 2.00
>
>   The author(s) grant permission for redistribution and use in source and
> binary forms, with or without modification, of the software and documentation
> provided that the following conditions are met:
>
> 0. If you receive a version of the software that is specifically labelled
>    as not being for redistribution (check the version message and/or README),
>    you are not permitted to redistribute that version of the software in any
>    way or form.
> 1. All terms of the all other applicable copyrights and licenses must be
>    followed.
> 2. Redistributions of source code must retain the authors' copyright
>    notice(s), this list of conditions, and the following disclaimer.
> 3. Redistributions in binary form must reproduce the authors' copyright
>    notice(s), this list of conditions, and the following disclaimer in the
>    documentation and/or other materials provided with the distribution.
> 4. [The copyright holder has authorized the removal of this clause.]
> 5. Neither the name(s) of the author(s) nor the names of its contributors
>    may be used to endorse or promote products derived from this software
>    without specific prior written permission.
>
> THIS SOFTWARE IS PROVIDED BY ITS AUTHORS AND CONTRIBUTORS ``AS IS'' AND ANY
> EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
> WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
> DISCLAIMED. IN NO EVENT SHALL THE AUTHORS OR CONTRIBUTORS BE LIABLE FOR ANY
> DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
> (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
> LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON
> ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
> (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
> SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
>
>   If these license terms cause you a real problem, contact the author.  */
>
> The file sunrpc/des_impl.c is copyright Eric Young:
>
> Copyright (C) 1992 Eric Young
> Collected from libdes and modified for SECURE RPC by Martin Kuck 1994
> This file is distributed under the terms of the GNU Lesser General
> Public License, version 2.1 or later - see the file COPYING.LIB for details.
> If you did not receive a copy of the license with this program, please
> see <http://www.gnu.org/licenses/> to obtain a copy.
>
> The libidn code is copyright Simon Josefsson, with portions copyright
> The Internet Society, Tom Tromey and Red Hat, Inc.:
>
> Copyright (C) 2002, 2003, 2004, 2011  Simon Josefsson
>
> This file is part of GNU Libidn.
>
> GNU Libidn is free software; you can redistribute it and/or
> modify it under the terms of the GNU Lesser General Public
> License as published by the Free Software Foundation; either
> version 2.1 of the License, or (at your option) any later version.
>
> GNU Libidn is distributed in the hope that it will be useful,
> but WITHOUT ANY WARRANTY; without even the implied warranty of
> MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
> Lesser General Public License for more details.
>
> You should have received a copy of the GNU Lesser General Public
> License along with GNU Libidn; if not, see <http://www.gnu.org/licenses/>.
>
> The following notice applies to portions of libidn/nfkc.c:
>
> This file contains functions from GLIB, including gutf8.c and
> gunidecomp.c, all licensed under LGPL and copyright hold by:
>
> Copyright (C) 1999, 2000 Tom Tromey
> Copyright 2000 Red Hat, Inc.
>
> The following applies to portions of libidn/punycode.c and
> libidn/punycode.h:
>
> This file is derived from RFC 3492bis written by Adam M. Costello.
>
> Disclaimer and license: Regarding this entire document or any
> portion of it (including the pseudocode and C code), the author
> makes no guarantees and is not responsible for any damage resulting
> from its use.  The author grants irrevocable permission to anyone
> to use, modify, and distribute it in any way that does not diminish
> the rights of anyone else to use, modify, and distribute it,
> provided that redistributed derivative works do not contain
> misleading author or version information.  Derivative works need
> not be licensed under similar terms.
>
> Copyright (C) The Internet Society (2003).  All Rights Reserved.
>
> This document and translations of it may be copied and furnished to
> others, and derivative works that comment on or otherwise explain it
> or assist in its implementation may be prepared, copied, published
> and distributed, in whole or in part, without restriction of any
> kind, provided that the above copyright notice and this paragraph are
> included on all such copies and derivative works.  However, this
> document itself may not be modified in any way, such as by removing
> the copyright notice or references to the Internet Society or other
> Internet organizations, except as needed for the purpose of
> developing Internet standards in which case the procedures for
> copyrights defined in the Internet Standards process must be
> followed, or as required to translate it into languages other than
> English.
>
> The limited permissions granted above are perpetual and will not be
> revoked by the Internet Society or its successors or assigns.
>
> This document and the information contained herein is provided on an
> "AS IS" basis and THE INTERNET SOCIETY AND THE INTERNET ENGINEERING
> TASK FORCE DISCLAIMS ALL WARRANTIES, EXPRESS OR IMPLIED, INCLUDING
> BUT NOT LIMITED TO ANY WARRANTY THAT THE USE OF THE INFORMATION
> HEREIN WILL NOT INFRINGE ANY RIGHTS OR ANY IMPLIED WARRANTIES OF
> MERCHANTABILITY OR FITNESS FOR A PARTICULAR PURPOSE.
>
> The file inet/rcmd.c is under a UCB copyright and the following:
>
> Copyright (C) 1998 WIDE Project.
> All rights reserved.
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions
> are met:
> 1. Redistributions of source code must retain the above copyright
>    notice, this list of conditions and the following disclaimer.
> 2. Redistributions in binary form must reproduce the above copyright
>    notice, this list of conditions and the following disclaimer in the
>    documentation and/or other materials provided with the distribution.
> 3. Neither the name of the project nor the names of its contributors
>    may be used to endorse or promote products derived from this software
>    without specific prior written permission.
>
> THIS SOFTWARE IS PROVIDED BY THE PROJECT AND CONTRIBUTORS ``AS IS'' AND
> ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
> IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
> ARE DISCLAIMED.  IN NO EVENT SHALL THE PROJECT OR CONTRIBUTORS BE LIABLE
> FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
> DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
> OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
> HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
> LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
> OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
> SUCH DAMAGE.
>
> The file posix/runtests.c is copyright Tom Lord:
>
> Copyright 1995 by Tom Lord
>
>                         All Rights Reserved
>
> Permission to use, copy, modify, and distribute this software and its
> documentation for any purpose and without fee is hereby granted,
> provided that the above copyright notice appear in all copies and that
> both that copyright notice and this permission notice appear in
> supporting documentation, and that the name of the copyright holder not be
> used in advertising or publicity pertaining to distribution of the
> software without specific, written prior permission.
>
> Tom Lord DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE,
> INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS, IN NO
> EVENT SHALL TOM LORD BE LIABLE FOR ANY SPECIAL, INDIRECT OR
> CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF
> USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR
> OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR
> PERFORMANCE OF THIS SOFTWARE.
>
> The posix/rxspencer tests are copyright Henry Spencer:
>
> Copyright 1992, 1993, 1994, 1997 Henry Spencer.  All rights reserved.
> This software is not subject to any license of the American Telephone
> and Telegraph Company or of the Regents of the University of California.
>
> Permission is granted to anyone to use this software for any purpose on
> any computer system, and to alter it and redistribute it, subject
> to the following restrictions:
>
> 1. The author is not responsible for the consequences of use of this
>    software, no matter how awful, even if they arise from flaws in it.
>
> 2. The origin of this software must not be misrepresented, either by
>    explicit claim or by omission.  Since few users ever read sources,
>    credits must appear in the documentation.
>
> 3. Altered versions must be plainly marked as such, and must not be
>    misrepresented as being the original software.  Since few users
>    ever read sources, credits must appear in the documentation.
>
> 4. This notice may not be removed or altered.
>
> The file posix/PCRE.tests is copyright University of Cambridge:
>
> Copyright (c) 1997-2003 University of Cambridge
>
> Permission is granted to anyone to use this software for any purpose on any
> computer system, and to redistribute it freely, subject to the following
> restrictions:
>
> 1. This software is distributed in the hope that it will be useful,
>    but WITHOUT ANY WARRANTY; without even the implied warranty of
>    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
>
> 2. The origin of this software must not be misrepresented, either by
>    explicit claim or by omission. In practice, this means that if you use
>    PCRE in software that you distribute to others, commercially or
>    otherwise, you must put a sentence like this
>
>      Regular expression support is provided by the PCRE library package,
>      which is open source software, written by Philip Hazel, and copyright
>      by the University of Cambridge, England.
>
>    somewhere reasonably visible in your documentation and in any relevant
>    files or online help data or similar. A reference to the ftp site for
>    the source, that is, to
>
>      ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/
>
>    should also be given in the documentation. However, this condition is not
>    intended to apply to whole chains of software. If package A includes PCRE,
>    it must acknowledge it, but if package B is software that includes package
>    A, the condition is not imposed on package B (unless it uses PCRE
>    independently).
>
> 3. Altered versions must be plainly marked as such, and must not be
>    misrepresented as being the original software.
>
> 4. If PCRE is embedded in any software that is released under the GNU
>   General Purpose Licence (GPL), or Lesser General Purpose Licence (LGPL),
>   then the terms of that licence shall supersede any condition above with
>   which it is incompatible.
>
> Files from Sun fdlibm are copyright Sun Microsystems, Inc.:
>
> Copyright (C) 1993 by Sun Microsystems, Inc. All rights reserved.
>
> Developed at SunPro, a Sun Microsystems, Inc. business.
> Permission to use, copy, modify, and distribute this
> software is freely granted, provided that this notice
> is preserved.
>
> Part of stdio-common/tst-printf.c is copyright C E Chew:
>
> (C) Copyright C E Chew
>
> Feel free to copy, use and distribute this software provided:
>
>      1. you do not pretend that you wrote it
>      2. you leave this copyright notice intact.
>
> Various long double libm functions are copyright Stephen L. Moshier:
>
> Copyright 2001 by Stephen L. Moshier <moshier@na-net.ornl.gov>
>
> This library is free software; you can redistribute it and/or
> modify it under the terms of the GNU Lesser General Public
> License as published by the Free Software Foundation; either
> version 2.1 of the License, or (at your option) any later version.
>
> This library is distributed in the hope that it will be useful,
> but WITHOUT ANY WARRANTY; without even the implied warranty of
> MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
> Lesser General Public License for more details.
>
> You should have received a copy of the GNU Lesser General Public
> License along with this library; if not, see
> <http://www.gnu.org/licenses/>.  */
> ```

### Nginx

From [Nginx's `LICENSE`][nginx-license]:

> ```
> /*
>  * Copyright (C) 2002-2018 Igor Sysoev
>  * Copyright (C) 2011-2018 Nginx, Inc.
>  * All rights reserved.
>  *
>  * Redistribution and use in source and binary forms, with or without
>  * modification, are permitted provided that the following conditions
>  * are met:
>  * 1. Redistributions of source code must retain the above copyright
>  *    notice, this list of conditions and the following disclaimer.
>  * 2. Redistributions in binary form must reproduce the above copyright
>  *    notice, this list of conditions and the following disclaimer in the
>  *    documentation and/or other materials provided with the distribution.
>  *
>  * THIS SOFTWARE IS PROVIDED BY THE AUTHOR AND CONTRIBUTORS ``AS IS'' AND
>  * ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
>  * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
>  * ARE DISCLAIMED.  IN NO EVENT SHALL THE AUTHOR OR CONTRIBUTORS BE LIABLE
>  * FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
>  * DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
>  * OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
>  * HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
>  * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
>  * OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
>  * SUCH DAMAGE.
>  */
> ```

#### `ngx_headers_more`

From [`ngx_headers_more`'s README][ngx_headers_more-license]:

> ```
> The code base is borrowed directly from the standard headers module in
> Nginx 0.8.24. This part of code is copyrighted by Igor Sysoev.
>
> Copyright (c) 2009-2014, Yichun "agentzh" Zhang (章亦春)
> agentzh@gmail.com, CloudFlare Inc.
>
> Copyright (c) 2010-2013, Bernd Dorn.
>
> This module is licensed under the terms of the BSD license.
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions are
> met:
>
> * Redistributions of source code must retain the above copyright
>   notice, this list of conditions and the following disclaimer.
> * Redistributions in binary form must reproduce the above copyright
>   notice, this list of conditions and the following disclaimer in the
>   documentation and/or other materials provided with the distribution.
>
> THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
> "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
> LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
> A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
> HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
> SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
> LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
> DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
> THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
> (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
> OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
> ```

### OpenSSL

From [OpenSSL's `LICENSE`][openssl-license]:

> ```
>   LICENSE ISSUES
>   ==============
>
>   The OpenSSL toolkit stays under a double license, i.e. both the conditions of
>   the OpenSSL License and the original SSLeay license apply to the toolkit.
>   See below for the actual license texts. Actually both licenses are BSD-style
>   Open Source licenses. In case of any license issues related to OpenSSL
>   please contact openssl-core@openssl.org.
>
>   OpenSSL License
>   ---------------
>
> /* ====================================================================
>  * Copyright (c) 1998-2017 The OpenSSL Project.  All rights reserved.
>  *
>  * Redistribution and use in source and binary forms, with or without
>  * modification, are permitted provided that the following conditions
>  * are met:
>  *
>  * 1. Redistributions of source code must retain the above copyright
>  *    notice, this list of conditions and the following disclaimer.
>  *
>  * 2. Redistributions in binary form must reproduce the above copyright
>  *    notice, this list of conditions and the following disclaimer in
>  *    the documentation and/or other materials provided with the
>  *    distribution.
>  *
>  * 3. All advertising materials mentioning features or use of this
>  *    software must display the following acknowledgment:
>  *    "This product includes software developed by the OpenSSL Project
>  *    for use in the OpenSSL Toolkit. (http://www.openssl.org/)"
>  *
>  * 4. The names "OpenSSL Toolkit" and "OpenSSL Project" must not be used to
>  *    endorse or promote products derived from this software without
>  *    prior written permission. For written permission, please contact
>  *    openssl-core@openssl.org.
>  *
>  * 5. Products derived from this software may not be called "OpenSSL"
>  *    nor may "OpenSSL" appear in their names without prior written
>  *    permission of the OpenSSL Project.
>  *
>  * 6. Redistributions of any form whatsoever must retain the following
>  *    acknowledgment:
>  *    "This product includes software developed by the OpenSSL Project
>  *    for use in the OpenSSL Toolkit (http://www.openssl.org/)"
>  *
>  * THIS SOFTWARE IS PROVIDED BY THE OpenSSL PROJECT ``AS IS'' AND ANY
>  * EXPRESSED OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
>  * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
>  * PURPOSE ARE DISCLAIMED.  IN NO EVENT SHALL THE OpenSSL PROJECT OR
>  * ITS CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
>  * SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT
>  * NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
>  * LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
>  * HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
>  * STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
>  * ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED
>  * OF THE POSSIBILITY OF SUCH DAMAGE.
>  * ====================================================================
>  *
>  * This product includes cryptographic software written by Eric Young
>  * (eay@cryptsoft.com).  This product includes software written by Tim
>  * Hudson (tjh@cryptsoft.com).
>  *
>  */
>
>  Original SSLeay License
>  -----------------------
>
> /* Copyright (C) 1995-1998 Eric Young (eay@cryptsoft.com)
>  * All rights reserved.
>  *
>  * This package is an SSL implementation written
>  * by Eric Young (eay@cryptsoft.com).
>  * The implementation was written so as to conform with Netscapes SSL.
>  *
>  * This library is free for commercial and non-commercial use as long as
>  * the following conditions are aheared to.  The following conditions
>  * apply to all code found in this distribution, be it the RC4, RSA,
>  * lhash, DES, etc., code; not just the SSL code.  The SSL documentation
>  * included with this distribution is covered by the same copyright terms
>  * except that the holder is Tim Hudson (tjh@cryptsoft.com).
>  *
>  * Copyright remains Eric Young's, and as such any Copyright notices in
>  * the code are not to be removed.
>  * If this package is used in a product, Eric Young should be given attribution
>  * as the author of the parts of the library used.
>  * This can be in the form of a textual message at program startup or
>  * in documentation (online or textual) provided with the package.
>  *
>  * Redistribution and use in source and binary forms, with or without
>  * modification, are permitted provided that the following conditions
>  * are met:
>  * 1. Redistributions of source code must retain the copyright
>  *    notice, this list of conditions and the following disclaimer.
>  * 2. Redistributions in binary form must reproduce the above copyright
>  *    notice, this list of conditions and the following disclaimer in the
>  *    documentation and/or other materials provided with the distribution.
>  * 3. All advertising materials mentioning features or use of this software
>  *    must display the following acknowledgement:
>  *    "This product includes cryptographic software written by
>  *     Eric Young (eay@cryptsoft.com)"
>  *    The word 'cryptographic' can be left out if the rouines from the library
>  *    being used are not cryptographic related :-).
>  * 4. If you include any Windows specific code (or a derivative thereof) from
>  *    the apps directory (application code) you must include an acknowledgement:
>  *    "This product includes software written by Tim Hudson (tjh@cryptsoft.com)"
>  *
>  * THIS SOFTWARE IS PROVIDED BY ERIC YOUNG ``AS IS'' AND
>  * ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
>  * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
>  * ARE DISCLAIMED.  IN NO EVENT SHALL THE AUTHOR OR CONTRIBUTORS BE LIABLE
>  * FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
>  * DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS
>  * OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
>  * HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
>  * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
>  * OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
>  * SUCH DAMAGE.
>  *
>  * The licence and distribution terms for any publically available version or
>  * derivative of this code cannot be changed.  i.e. this code cannot simply be
>  * copied and put under another distribution licence
>  * [including the GNU Public Licence.]
>  */
> ```

### PCRE2

From [PCRE2's `LICENSE`][pcre-license]:

> ```
> PCRE2 LICENCE
> -------------
>
> PCRE2 is a library of functions to support regular expressions whose syntax
> and semantics are as close as possible to those of the Perl 5 language.
>
> Release 10 of PCRE2 is distributed under the terms of the "BSD" licence, as
> specified below, with one exemption for certain binary redistributions. The
> documentation for PCRE2, supplied in the "doc" directory, is distributed under
> the same terms as the software itself. The data in the testdata directory is
> not copyrighted and is in the public domain.
>
> The basic library functions are written in C and are freestanding. Also
> included in the distribution is a just-in-time compiler that can be used to
> optimize pattern matching. This is an optional feature that can be omitted when
> the library is built.
>
> THE BASIC LIBRARY FUNCTIONS
> ---------------------------
>
> Written by:       Philip Hazel
> Email local part: ph10
> Email domain:     cam.ac.uk
>
> University of Cambridge Computing Service,
> Cambridge, England.
>
> Copyright (c) 1997-2018 University of Cambridge
> All rights reserved.
>
> PCRE2 JUST-IN-TIME COMPILATION SUPPORT
> --------------------------------------
>
> Written by:       Zoltan Herczeg
> Email local part: hzmester
> Emain domain:     freemail.hu
>
> Copyright(c) 2010-2018 Zoltan Herczeg
> All rights reserved.
>
> STACK-LESS JUST-IN-TIME COMPILER
> --------------------------------
>
> Written by:       Zoltan Herczeg
> Email local part: hzmester
> Emain domain:     freemail.hu
>
> Copyright(c) 2009-2018 Zoltan Herczeg
> All rights reserved.
>
>
> THE "BSD" LICENCE
> -----------------
>
> Redistribution and use in source and binary forms, with or without
> modification, are permitted provided that the following conditions are met:
>
>     * Redistributions of source code must retain the above copyright notices,
>       this list of conditions and the following disclaimer.
>
>     * Redistributions in binary form must reproduce the above copyright
>       notices, this list of conditions and the following disclaimer in the
>       documentation and/or other materials provided with the distribution.
>
>     * Neither the name of the University of Cambridge nor the names of any
>       contributors may be used to endorse or promote products derived from this
>       software without specific prior written permission.
>
> THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
> AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
> IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
> ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE
> LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
> CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
> SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
> INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
> CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
> ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
> POSSIBILITY OF SUCH DAMAGE.
>
> EXEMPTION FOR BINARY LIBRARY-LIKE PACKAGES
> ------------------------------------------
>
> The second condition in the BSD licence (covering binary redistributions) does
> not apply all the way down a chain of software. If binary package A includes
> PCRE2, it must respect the condition, but if package B is software that
> includes package A, the condition is not imposed on package B unless it uses
> PCRE2 independently.
>
> End
> ```

### zlib

zlib is distributed under the [zlib license][zlib-license], which does
not require duplication alongside binary distribution.

[ccon]: https://github.com/wking/ccon
[gentoo-nginx]: https://packages.gentoo.org/packages/www-servers/nginx
[glibc-licenses]: https://sourceware.org/git/?p=glibc.git;a=blob;f=LICENSES;h=80f7f1487947f57815b9fe076fadc8c7f94eeb8e;hb=db0242e3023436757bbc7c488a779e6e3343db04
[iptables]: https://www.netfilter.org/projects/iptables/
[lddtree-builder]: https://github.com/wking/lddtree-builder
[LGPL-2.1]: https://www.gnu.org/licenses/old-licenses/lgpl-2.1.en.html
[listen]: https://nginx.org/en/docs/http/ngx_http_core_module.html#listen
[Nginx]: https://nginx.org/
[nginx-license]: https://nginx.org/LICENSE
[ngx_headers_more-license]: https://github.com/openresty/headers-more-nginx-module/tree/v0.32#copyright--license
[openssl-license]: https://github.com/openssl/openssl/blob/OpenSSL_1_0_2n/LICENSE
[pcre-license]: https://vcs.pcre.org/pcre2/code/trunk/LICENCE?revision=910&view=markup
[Portage]: https://wiki.gentoo.org/wiki/Portage
[zlib-license]: https://github.com/madler/zlib/blob/v1.2.11/README#L87-L115

[capabilities.7]: http://man7.org/linux/man-pages/man7/capabilities.7.html

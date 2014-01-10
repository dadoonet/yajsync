yajsync
=======

yajsync is a port of [rsync](http://rsync.samba.org) written in Java.

yajsync currently supports a minimal subset of rsync protocol version
30.0, with the additional constraint that the peer must also support
rsync safe file lists.


Features
--------

- rsync Java API

- Platform independent rsync server

- Platform independent rsync client with support for both local and
  remote file transfers

Please be aware though that the API currently is unstable, not
documented and will most probably change in the near future


Warning
-------

This software is still unstable and there might be data corruption
bugs hiding. So use it only carefully at your own risk.


Example
-------

Start a Server listening on localhost port 14415, with one implicitly
read-only module called Downloads and one writable module called
Uploads:

```
$ cat yajsyncd.conf
[Downloads]
path = /path/to/Downloads/

[Uploads]
path = /path/to/Uploads/
read only = false

$ java -Dumask=$(umask) -jar build/jar/yajsyncd.jar --port=14415 --config=yajsyncd.conf
```

Recursively upload the directory called example to Uploads:

```
java -Dumask=$(umask) -jar build/jar/yajsync.jar --port=14415 -r example localhost::Uploads
```

The same thing using the alternative syntax:
```
java -Dumask=$(umask) -jar build/jar/yajsync.jar-r example rsync://localhost:14415/Uploads
```

And finally the same thing using the original rsync client:
```
rsync --port=14415 -r example localhost::Uploads
```


Note
----

- recursive transfers always implies incremental recursion

- use ```--charset``` for setting common character set (defaults to
  UTF-8). Note that ```--iconv``` is _not_ supported

- client local file transfers always uses rsync:s delta transfer
  algorithm, i.e. it does not have an option ```--whole-file```

- checksum block lengths are not computed in the same way as the
  original rsync - they are always a power of 2 and a minimum 512 of
  bytes


Extra feature
-------------

yajsync also adds the client/server local option ```--defer-write```
which makes the receiver avoid writing into a temporary file if the
target file is unchanged.


Build instructions
------------------

Requirements:

- [git](http://git-scm.com)
- [ant](http://ant.apache.org)
- [OpenJDK 1.7 or later](http://openjdk.java.net/) or [Oracle JDK 7](http://java.oracle.com)

Procedure:

    git clone https://github.com/perlundq/yajsync.git
    cd yajsync
    ant


Usage
-----

show client/server help (-h argument):

(Windows):

    java -jar build/jar/yajsync.jar  -h
    java -jar build/jar/yajsyncd.jar -h

(Unix/Linux, we must inject necessary umask information as a property,
assuming Bourne shell compatible syntax)

    java -Dumask=$(umask) -jar build/jar/yajsync.jar  -h
    java -Dumask=$(umask) -jar build/jar/yajsyncd.jar -h

Recommended extra options to the jvm (i.e. must be placed before the
-jar <jar-file> argument):

Turn on assertions:

    -ea
    
Use a more memory conservative garbage collector:

    -XX:+UseConcMarkSweepGC

Turn on aggressive optimisations:

    -XX:+AggressiveOpts


License
-------

Copyright (C) 1996-2011 by Andrew Tridgell, Wayne Davison, and others

Copyright (C) 2013,2014 Per Lundqvist

yajsync is licensed under GNU General Public License version 3 or
later. See the file LICENSE or http://www.gnu.org/licenses/gpl.txt for
the license details.
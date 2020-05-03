---
layout: post
title:  "A unexpected behaviour of pkg-config on MSYS2/MinGW64"
date:   2020-5-3 13:00:00 +0800
categories: pytorch pylint vscode flake8
---

The original openblas.pc was started with
```pc
libdir=/mingw64/lib
includedir=/mingw64/include
```

and the pkg-config outputs
```shell
$ pkg-config --cflags openblas
-I/mingw64/include
```

While glib-2.0.pc was started with
```pc
prefix=/mingw64
libdir=${prefix}/lib
includedir=${prefix}/include
```

and the pkg-config outputs
```shell
$ pkg-config --cflags glib-2.0
-ID:/msys64/mingw64/include/glib-2.0
```


If edit openblas.pc into glib-2.0 version, the output becomes
```shell
$ pkg-config --cflags openblas
-ID:/msys64/mingw64/include/OpenBLAS
```

Amazing!

Still don't know why.
*Meson build for the GNU Scientific Library*

This repository provides the official source code for the GSL library with the
addition of a Meson build.

Currently only version 2.1 is available. If you need a newer version you may
create a github issue with the request.

The source code corresponds exactly to the official release except that the public
header files are all located in the `include/gsl` directory. In the original code
the public headers are scattered in each module sub-directory alongside the
module's private headers.

## Why Meson ?

Meson is a modern build system simple to use and effective even for large projects.
In addition it provides a lot of useful options out-of-the-box and is cross-platform.

Meson is currently used by many free software projects, like for example [the Mesa 3D graphics library](https://www.mesa3d.org/).

I wrote the Meson build for the GSL library two reasons.

First was to allow usage of alternative BLAS library either as static or
shared library.

The standard configure script, when used on Windows, hard code the
name of the BLAS DLL library to gslcblas and prevent therefore the usage
of an alternative BLAS library.

Second reason was to increase the compilation time, especially on Windows
when using MinGW.
When using Meson the build time is greatly improved and gslcblas is not compiled
altogether is it is not required reducing further the build time.

## How to use it?

From the source directory:

```sh
meson setup build

# To use an alternative BLAS library like for example openblas use:
#
# meson setup -Dblas=openblas build
# If the BLAS library is not specified the blas implementation provided
# by the GSL library will be used.

ninja -C build
ninja -C build install
```
more information from the [Meson Quick guide](https://mesonbuild.com/Quick-guide.html).

# GSL - GNU Scientific Library (from original README)

This is GSL, the GNU Scientific Library, a collection of numerical
routines for scientific computing.

GSL is free software, you can redistribute it and/or modify it under
the terms of the GNU General Public License.

The GNU General Public License does not permit this software to be
redistributed in proprietary programs.

This library is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

### Availability

The current stable version of GSL is always available from ftp.gnu.org
in the directory /pub/gnu/gsl.

A list of mirror sites can be found at http://www.gnu.org/order/ftp.html

### Installation

GSL follows the standard GNU installation procedure.  Please consult
the INSTALL file in this distribution for more detailed instructions.

For information about specific platforms and compilers see the
"Compilation Notes" section in the INSTALL file.

### More information about GSL

The project homepage is http://www.gnu.org/software/gsl/

See the NEWS file for recent changes to the library.

The GSL Manual has been published and can be ordered from most
bookstores. The publication details are,

  GNU Scientific Library Reference Manual - Revised Second Edition, 
  M. Galassi et al, ISBN 0954161734 (620 pages, paperback).

The money raised from sales of the manual helps support the
development of GSL.

A Japanese translation of the reference manual is available from the
GSL website above (thanks to Daisuke TOMINAGA).

### Reporting Bugs

A list of known bugs can be found in the BUGS file.  Details of
compilation problems can be found in the INSTALL file.

If you find a bug which is not listed in these files please report it
to bug-gsl@gnu.org.

All bug reports should include:

       The version number of GSL, and where you obtained it.
       The hardware and operating system
       The compiler used, including version number and compilation options
       A description of the bug behaviour
       A short program which reproducibly exercises the bug

It is useful if you can check whether the same problem occurs when the
library is compiled without optimization.  Thank you.

Any errors or omissions in the manual can also be reported to the
same address.

### Contributing to GSL

If you are interested in participating in GSL development, please see
the webpage at http://www.gnu.org/software/gsl/


# TinyThread++ v1.1 #
---------------------

http://tinythreadpp.bitsnbites.eu


## About ##
-----------

`TinyThread++` is a minimalist, portable, threading library for C++, intended to
make it easy to create multi threaded C++ applications.

The library is closesly modeled after the C++11 standard, but only a subset is
implemented at the moment.

See the documentation in the `doc/html` directory for more information.


## Using TinyThread++ ##
------------------------

To use `TinyThread++` in your own project, just add `tinythread.cpp` and
`tinythread.h` to your project. In your own code, do:

```c
#include <tinythread.h>
using namespace tthread;
```

If you wish to use the `fast_mutex` class, inlude `fast_mutex.h`:

```c
#include <fast_mutex.h>
```


## Building the test programs ##
--------------------------------

From the test folder, issue one of the following commands:

Linux, Mac OS X, OpenSolaris etc:

- make   (you may need to use gmake on some systems)

Windows/MinGW:

- mingw32-make

Windows/MS Visual Studio:

- nmake /f Makefile.msvc



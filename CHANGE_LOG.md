# Changelog #

-------------

- v1.2 - 2021.07.11
    - Added `thread::kill()`

- v1.2 - 2012.11.07
    - Fixed dangling references bug in `thread::detach()` mechanism.
    - Added `atomic<T>` class.
    - Added `atomic_flag` class.

- v1.1 - 2012.05.07
    - Added `thread::detach()`.

- v1.0 - 2010.10.01
    - First non-beta release.
    - Made mutex non-recursive (according to spec), and added `recursive_mutex`.
    - General class, code & documentation improvements.
    - Added a Makefile for `MS Visual Studio`.

- v0.9 - 2010.08.10
    - Added preliminary support for `this_thread::sleep_for()`.

- v0.8 - 2010.07.02
    - Switched from `CreateThread()` to `_beginthreadex()` for Win32 (should fix
      tiny memory leaks).
    - Better standards compliance and some code cleanup.

- v0.7 - 2010.05.17
    - Added `this_thread::yield()`.
    - Replaced the non-standard `number_of_processors()` function with
      `thread::hardware_concurrency()`, which is part of the `C++0x` draft.
    - The `thread::id()` class is now more standards compliant (correct namespace
      and comparison operators).

- v0.6 - 2010.04.28
    - Added a `fast_mutex` class (in `fast_mutex.h`.)
    - Made the `test.cpp` application compile under `Mac OS X` and `MinGW/g++ 3.x`.

- v0.5 - 2010.03.31
    - Added the `thread_local` keyword (support for thread-local storage).
    - Added a test application to test the API (`test.cpp`.)
    - Improved the Doxygen documentation.

- v0.4 - 2010.03.27
    - Added `thread::get_id()` and `this_thread::get_id()`.
    - Changed the namespace name from tinythread to tthread.

- v0.3 - 2010.03.24
    - Fixed a compiler error for `fractal.cpp` under `MS Visual C++`.
    - Added colors to the fractal generator.

- v0.2 - 2010.03.23
    - Better `C++0x` conformance.
    - Better documentation.
    - New classes:
        - `lock_guard`
    - New member functions:
        - `thread::joinable()`
        - `thread::native_handle()`
        - `mutex::try_lock()`
    - Added a multi threaded fractal generator test application.

- v0.1 - 2010.03.21
    - Initial release.



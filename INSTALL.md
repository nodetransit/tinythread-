## Install ##

-------------

### Compiling ###

```sh
mkdir obj
cd obj
cmake ../test
make
```

### Options ###

| option | default | | |
| --- | --- | --- | --- |
| DEBUG | 0 | compile with debug symbols | automatically set when `CMAKE_BUILD_TYPE` is `Debug` |
| TINYTHREAD_EXPORT_LIB | 1 | compile as a shared library | |
| VERBOSE | 0 | print compile and link commands during make | |


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
| DEBUG | OFF | compile with debug symbols | automatically set when `CMAKE_BUILD_TYPE` is `Debug` |
| TINYTHREAD_EXPORT_LIB | ON | compile as a shared library | |
| VERBOSE | OFF | print compile and link commands during make | |


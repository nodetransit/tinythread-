cmake_minimum_required (VERSION 3.16)
project (tinythread VERSION 1.2.1 DESCRIPTION "Minimalist, portable threading library.")

include (CheckIncludeFiles)

set (CMAKE_CXX_STANDARD 11)

#---------------------------------------------------------------------

option (DEBUG "Compile with debug symbols." OFF)
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    set (DEBUG ON)
endif ()

if (NOT DEBUG)
    set (RELEASE ON)
    set (DEBUG OFF)
    set (_DEBUG OFF)
    set (NDEBUG ON)
else ()
    set (RELEASE OFF)
    set (DEBUG ON)
    set (_DEBUG ON)
    set (NDEBUG OFF)
endif ()

if (CMAKE_BUILD_TYPE STREQUAL "RelWithDebInfo")
    set (DEBUGINFO ON)
endif ()

option (VERBOSE "Verbose console output." OFF)
if (VERBOSE)
    set (CMAKE_VERBOSE_MAKEFILE ON)
endif ()

option (TINYTHREAD_EXPORT_LIB "Build Shared Libraries." ON)
if (TINYTHREAD_EXPORT_LIB)
    set (TINYTHREAD_LIB_TYPE SHARED)
else ()
    set (TINYTHREAD_LIB_TYPE STATIC)
endif ()

#---------------------------------------------------------------------

if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    set (LINUX ON)
endif ()

if (WIN32)
    # operating system for the spoiled and the losers
    set (LOSER ON)
    message (STATUS "No exports, forcing static linkage.")
    set (TINYTHREAD_EXPORT_LIB OFF)
    set (TINYTHREAD_LIB_TYPE STATIC)
endif ()
# no support for the weirdos using APPLE

find_package(Threads REQUIRED)
#if (NOT Threads)
#    message (FATAL_ERROR "cannot find pthreads")
#endif ()

if (LOSE)
    if (MSVC)
        set (DLLTOOL OFF)
    else ()
        message (STATUS "Detecting dlltool")
        find_program (DLLTOOL dlltool)
        if (DLLTOOL)
            message (STATUS "  Found dlltool: ${DLLTOOL}")
        else ()
            message (WARNING "dlltool not found. Skipping import library generation.")
        endif ()
    endif ()

    if (MINGW)
        set (CMAKE_RC_COMPILER_INIT windres)
        enable_language (RC)
        set (CMAKE_RC_COMPILE_OBJECT "<CMAKE_RC_COMPILER> <DEFINES> -o <OBJECT> -i <SOURCE>")
    endif ()

endif ()

#---------------------------------------------------------------------

file (GLOB_RECURSE SOURCE_FILES "." "*.cpp")

set (BINARY_NAME "tthreadpp")
add_library (${BINARY_NAME} ${TINYTHREAD_LIB_TYPE} ${SOURCE_FILES})

#---------------------------------------------------------------------

set (COMMON_COMPILE_OPTIONS "")
set (COMMON_DEBUG_COMPILE_OPTTIONS "")

if (NOT MSVC)
    list (APPEND COMMON_COMPILE_OPTIONS "-Wno-missing-braces"
                                        "-Wall"
                                        "-Wextra"
                                        #-Wfatal-errors
                                        "-fno-strict-aliasing")
    #add_definitions ("--pedantic"
    #                 "-ansi")

    if (DEBUG)
        list (APPEND COMMON_DEBUG_COMPILE_OPTTIONS #"-Wno-bool-compare"
                                                   #"-Wno-incompatible-pointer-types"
                                                   "-Wno-unused-parameter"
                                                   #"-Wno-unused-variable"
                                                   "-Wno-unused-function"
                                                   "-Wno-format")
    endif ()

endif ()

if (DEBUG OR DEBUGINFO)
    message (STATUS "Compiling with DEBUG symbols")

    if (NOT MSVC)
        add_definitions (-g3
                         -gz=zlib
                         -fdiagnostics-show-option
                         -femit-class-debug-always
                         -fvar-tracking
                         -fmax-errors=3)
    else ()
        add_definitions ("-ZI")
    endif ()
endif ()

if (NOT DEBUG)
    message (STATUS "Compiling with optimizations")

    if (NOT MSVC)
        list (APPEND COMMON_COMPILE_OPTIONS "-O3"
                                            "-s"
                                            "-c"
                                            "-D_FORTIFY_SOURCE=2")
    else ()
        list (APPEND COMMON_COMPILE_OPTIONS "-Ox"
                                            "-GF"
                                            "-c"
                                            "-W2"
                                            "-GL")
    endif ()
endif ()

#---------------------------------------------------------------------

if (${TINYTHREAD_LIB_TYPE} STREQUAL "STATIC")
    list (APPEND COMMON_DEBUG_COMPILE_OPTTIONS "-fPIC"
          )
elseif (${TINYTHREAD_LIB_TYPE} STREQUAL "SHARED")
    if (LOSER)
        set (CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

        if (MSVC)
            set_target_properties (${BINARY_NAME} PROPERTIES OUTPUT_NAME "${BINARY_NAME}"
                                                  ARCHIVE_OUTPUT_NAME "${BINARY_NAME}"
                                                  LINK_FLAGS "${CMAKE_SHARED_LINKER_FLAGS_INIT} \/DYNAMICBASE")
        endif ()

        if (DLLTOOL)
            set_target_properties (${BINARY_NAME} PROPERTIES OUTPUT_NAME "${BINARY_NAME}"
                                                  ARCHIVE_OUTPUT_NAME "${BINARY_NAME}"
                                                  LINK_FLAGS "${CMAKE_SHARED_LINKER_FLAGS_INIT} -Wl,--output-def=${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/lib${BINARY_NAME}.def")

            add_custom_command(TARGET ${BINARY_NAME} POST_BUILD
                               WORKING_DIRECTORY "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}"
                               COMMAND echo "Generating import library"
                               COMMAND "${DLLTOOL}" --dllname "lib${BINARY_NAME}.dll"
                                                    --input-def "lib${BINARY_NAME}.def"
                                                    --output-lib "lib${BINARY_NAME}.lib"
                                                    #todo add --as-flags=--64
                                                    )

        endif ()
    endif ()

    #if (LINUX)
    #    set_target_properties (${BINARY_NAME} PROPERTIES OUTPUT_NAME "${BINARY_NAME}"
    #                           ARCHIVE_OUTPUT_NAME "${BINARY_NAME}"
    #                           LINK_FLAGS ${CMAKE_SHARED_LINKER_FLAGS_INIT}
    #                                      "-Wl,-rpath=./")
    #
    #endif ()

    set_target_properties(${BINARY_NAME} PROPERTIES VERSION ${PROJECT_VERSION})
    set_target_properties(${BINARY_NAME} PROPERTIES SOVERSION 1)
endif ()

if (LINUX)
    target_link_libraries (${BINARY_NAME} "pthread")
endif ()

target_compile_options (${BINARY_NAME} PRIVATE ${COMMON_COMPILE_OPTIONS}
                        ${COMMON_DEBUG_COMPILE_OPTTIONS})
set_property (TARGET ${BINARY_NAME} PROPERTY CXX_STANDARD 11)

target_include_directories(${BINARY_NAME}
                           PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
                                  "$<INSTALL_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>")

#---------------------------------------------------------------------

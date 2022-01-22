cmake_minimum_required(VERSION 3.18)

project(libao)

include(CheckIncludeFiles)
check_include_files("sys/audio.h" HAVE_SYS_AUDIO_H)

set(LIBAO_SOURCES
    src/ao_aixs.c
    src/ao_au.c
    src/ao_null.c
    src/ao_raw.c
    src/ao_wav.c
    src/audio_out.c
    src/config.c
)

if(WIN32)
    list(APPEND LIBAO_SOURCES src/ao_wmm.c)
endif()

add_library(libao ${LIBAO_SOURCES})
target_include_directories(libao PUBLIC include)
target_include_directories(libao PRIVATE include/ao)
target_compile_definitions(libao PRIVATE AO_BUILDING_LIBAO)

if(MSVC)
    target_compile_options(libao PRIVATE /w)
else()
    target_compile_options(libao PRIVATE -w)
endif()

if(WIN32)
    target_compile_definitions(libao PRIVATE HAVE_WMM)
    target_link_libraries(libao PRIVATE Winmm.lib ksuser)
endif()

if(HAVE_SYS_AUDIO_H)
    target_compile_definitions(libao PRIVATE HAVE_SYS_AUDIO_H)
endif()

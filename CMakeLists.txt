cmake_minimum_required(VERSION 3.1)
project(libftd2xx)

include(GNUInstallDirs)

set(FTD2XX_OS "Linux") # TODO: support multiple architectures and OSs
set(FTD2XX_VERSION "1.4.8")

if (${CMAKE_SYSTEM_PROCESSOR} STREQUAL "x86_64")
    set(FTD2XX_ARCH "x86_64")
    set(FTD2XX_SHA256 "815d880c5ec40904f062373e52de07b2acaa428e54fece98b31e6573f5d261a0")
elseif (${CMAKE_SYSTEM_PROCESSOR} MATCHES "i?86")
    set(FTD2XX_URL "i386")
    set(FTD2XX_SHA256 "84c9aaf7288a154faf0e2814ba590ec965141c698b2a00bffc94d8e0c7ebeb4c")
endif ()

set(FTD2XX_URL "https://www.ftdichip.com/Drivers/D2XX/${FTD2XX_OS}/libftd2xx-${FTD2XX_ARCH}-${FTD2XX_VERSION}.gz")
set(FTD2XX_FILENAME "libftd2xx-${FTD2XX_ARCH}-${FTD2XX_VERSION}.tar.gz")
set(FTD2XX_FILEPATH "./${FTD2XX_FILENAME}")

set(CPACK_COMPONENTS_GROUPING IGNORE)

set(CPACK_PACKAGE_NAME "libftd2xx")
set(CPACK_PACKAGE_VENDOR "Future Technology Devices International Ltd.")
set(CPACK_PACKAGE_VERSION "${FTD2XX_VERSION}")

set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Luca Cireddu")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "D2XX drivers for FTDI devices")
set(CPACK_GENERATOR "DEB")

execute_process(COMMAND dpkg --print-architecture
        OUTPUT_VARIABLE CPACK_DEBIAN_PACKAGE_ARCHITECTURE
        OUTPUT_STRIP_TRAILING_WHITESPACE)

set(CPACK_DEBIAN_PACKAGE_SECTION "misc")
set(CPACK_DEBIAN_PACKAGE_PRIORITY "optional")
set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS TRUE)

set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CPACK_DEBIAN_PACKAGE_ARCHITECTURE}")

include(CPack)

if (NOT EXISTS ${FTD2XX_FILEPATH})
    message(STATUS "Downloading libftd2xx: ${FTD2XX_URL}")
    file(DOWNLOAD "${FTD2XX_URL}" "${FTD2XX_FILEPATH}"
            EXPECTED_HASH "SHA256=${FTD2XX_SHA256}"
            SHOW_PROGRESS)
endif ()

message(STATUS "Extracting ${FTD2XX_FILENAME}")
execute_process(COMMAND ${CMAKE_COMMAND} -E tar xf "${FTD2XX_FILENAME}"
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/release/build/libftd2xx.so.${FTD2XX_VERSION}"
        DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        COMPONENT libftd2xx)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/release/build/libftd2xx.a"
        DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        COMPONENT libftd2xx)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/release/ftd2xx.h"
        DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
        COMPONENT libftd2xx)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/release/WinTypes.h"
        DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
        COMPONENT libftd2xx)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/release/release-notes.txt"
        DESTINATION "${CMAKE_INSTALL_DOCDIR}"
        COMPONENT libftd2xx)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/release/ReadMe.txt"
        DESTINATION "${CMAKE_INSTALL_DOCDIR}"
        COMPONENT libftd2xx)

cpack_add_component(libftd2xx
        DISPLAY_NAME "FTD2XX Library"
        DESCRIPTION "D2XX drivers allow direct access to the USB device through a DLL"
        REQUIRED)

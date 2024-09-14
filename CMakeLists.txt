project(libfreefare C)
cmake_minimum_required(VERSION 2.6)

SET(VERSION_MAJOR "0")
SET(VERSION_MINOR "4")
SET(VERSION_PATCH "0")

SET(VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")

SET(PACKAGE_NAME "libnfc")
SET(PACKAGE_VERSION ${VERSION})
SET(PACKAGE_STRING "${PACKAGE_NAME} ${PACKAGE_VERSION}")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/")

add_definitions("-std=c99")

find_package(LIBNFC REQUIRED)
find_package(OpenSSL REQUIRED)

include(GNUInstallDirs)

IF(WIN32)
  configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/config_windows.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/include/config.h)
  include_directories(${CMAKE_CURRENT_SOURCE_DIR}/contrib/win32)
  find_library(WINSOCK_LIB libws2_32.a)
  set(LIBS ${LIBS} ${WINSOCK_LIB})
ELSE(WIN32)
  include(CheckIncludeFiles)
  check_include_files("sys/endian.h" HAVE_SYS_ENDIAN_H)
  check_include_files("endian.h" HAVE_ENDIAN_H)
  check_include_files("byteswap.h" HAVE_BYTESWAP_H)
  check_include_files("CoreFoundation/CoreFoundation.h" HAVE_COREFOUNDATION_COREFOUNDATION_H)
  set(_XOPEN_SOURCE 600)
  configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/config_posix.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/include/config.h)
ENDIF(WIN32)

include_directories(${CMAKE_CURRENT_BINARY_DIR}/include/)
add_definitions("-DHAVE_CONFIG_H")

if(MINGW AND CMAKE_HOST_SYSTEM_NAME STREQUAL "Windows")
  # force MinGW-w64 in 32bit mode
  add_definitions("-m32")
  add_definitions("-DNOCRYPT")
  set(CMAKE_SHARED_LINKER_FLAGS -m32)
  set(CMAKE_EXE_LINKER_FLAGS -m32)
endif(MINGW)

message("CMAKE_C_FLAGS: " ${CMAKE_C_FLAGS})
message("CMAKE_SHARED_LINKER_FLAGS: " ${CMAKE_SHARED_LINKER_FLAGS})

include_directories(${LIBNFC_INCLUDE_DIRS} ${OPENSSL_INCLUDE_DIR} ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/contrib/libutil)
set(LIBS ${LIBS} ${LIBNFC_LIBRARIES} ${OPENSSL_LIBRARIES})

option(WITH_DEBUG "Extra debug information is outputted when this is turned on" OFF)

set(prefix ${CMAKE_INSTALL_PREFIX})
set(exec_prefix \${prefix})
set(includedir \${prefix}/${CMAKE_INSTALL_INCLUDEDIR})
set(libdir \${prefix}/${CMAKE_INSTALL_LIBDIR})
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libfreefare.pc.in ${CMAKE_CURRENT_BINARY_DIR}/libfreefare.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libfreefare.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)

add_subdirectory(libfreefare)
add_subdirectory(examples)

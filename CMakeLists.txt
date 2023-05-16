cmake_minimum_required(VERSION 3.0.2)

project(iddawc-sandbox)

include(ExternalProject)

##############################################################################
# orcania

set(ORCANIA_VERSION 2.3.2)
set(ORCANIA_CONFIG_FLAGS
  -DBUILD_BASE64URL=OFF
  -DBUILD_STATIC=ON
)
ExternalProject_Add(orcania
  URL https://github.com/babelouest/orcania/archive/refs/tags/v${ORCANIA_VERSION}.tar.gz
  PREFIX orcania-${ORCANIA_VERSION}
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR> ${ORCANIA_CONFIG_FLAGS}
)

ExternalProject_Get_property(orcania binary_dir install_dir source_dir)
set(orcania_BINARY_DIR ${binary_dir})
set(orcania_INSTALL_DIR ${install_dir})
set(orcania_SOURCE_DIR ${source_dir})

message(STATUS "orcania binary dir: ${orcania_BINARY_DIR}")
message(STATUS "orcania install dir: ${orcania_INSTALL_DIR}")
message(STATUS "orcania source dir: ${orcania_SOURCE_DIR}")

set(orcania_PREFIX_PATH ${CMAKE_BINARY_DIR}/orcania-${ORCANIA_VERSION}/lib64/pkgconfig)

##############################################################################
# yder

# external project install has not occured... despite appearances so the pkg_config_path is not set for the yder project to find orcania
# need to use external project download and execute process to sub-cmake commands...

set(YDER_VERSION 1.4.19)
set(YDER_CONFIG_FLAGS
  -DWITH_JOURNALD=OFF
  -DDOWNLOAD_DEPENDENCIES=OFF
  -DBUILD_STATIC=OFF
  -DCMAKE_PREFIX_PATH:PATH=${orcania_PREFIX_PATH}
)
ExternalProject_Add(yder
  URL https://github.com/babelouest/yder/archive/refs/tags/v${YDER_VERSION}.tar.gz
  PREFIX yder-${YDER_VERSION}
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=<INSTALL_DIR> ${YDER_CONFIG_FLAGS}
)
add_dependencies(yder orcania)

ExternalProject_Get_property(yder binary_dir install_dir source_dir)
set(yder_BINARY_DIR ${binary_dir})
set(yder_INSTALL_DIR ${install_dir})
set(yder_SOURCE_DIR ${source_dir})

message(STATUS "yder binary dir: ${yder_BINARY_DIR}")
message(STATUS "yder install dir: ${yder_INSTALL_DIR}")
message(STATUS "yder source dir: ${yder_SOURCE_DIR}")

##############################################################################
# libmicrohttpd

set(LIBMICROHTTPD_CONFIG_FLAGS)
ExternalProject_Add(libmicrohttpd
  URL https://ftp.gnu.org/gnu/libmicrohttpd/libmicrohttpd-0.9.76.tar.gz 
  CONFIGURE_COMMAND <SOURCE_DIR>/configure --prefix=<INSTALL_DIR> ${LIBMICROHTTPD_CONFIG_FLAGS}
  BUILD_COMMAND ${MAKE_EXECUTABLE} -j
  INSTALL_COMMAND ${MAKE_EXECUTABLE} -j install
  TEST_COMMAND ""
  BUILD_BYPRODUCTS libmicrohttpd.so
)



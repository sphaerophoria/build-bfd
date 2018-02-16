project(bfd)
cmake_minimum_required(VERSION 2.8)

include(ExternalProject)

set(EXTERNAL_PREFIX ${CMAKE_CURRENT_BINARY_DIR}/prefix)

execute_process (
  COMMAND ${CMAKE_C_COMPILER} -print-multiarch
  OUTPUT_VARIABLE HOST
)

ExternalProject_Add(binutils
	URL https://ftp.gnu.org/gnu/binutils/binutils-2.30.tar.xz
	DOWNLOAD_DIR ${CMAKE_CURRENT_BINARY_DIR}/binutils
	SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/binutils
	CONFIGURE_COMMAND ""
	BUILD_COMMAND ""
	INSTALL_COMMAND "")

function(build_binutils_project binutils_project libname)
	ExternalProject_Add(${binutils_project}
		DOWNLOAD_COMMAND ""
		DEPENDS binutils
		SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/binutils/${binutils_project}
		BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR}/binutils-build/${binutils_project}
		CONFIGURE_COMMAND CC=${CMAKE_C_COMPILER} CXX=${CMAKE_CXX_COMPILER} <SOURCE_DIR>/configure --prefix=${EXTERNAL_PREFIX} --host=${HOST} 
		INSTALL_DIR ${EXTERNAL_PREFIX}
		INSTALL_COMMAND make install && mkdir -p <INSTALL_DIR>/lib && cp <BINARY_DIR>/${libname}.a <INSTALL_DIR>/lib/)
endfunction()

build_binutils_project(zlib libz)
build_binutils_project(libiberty libiberty)
build_binutils_project(bfd libbfd)

install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/prefix/
  DESTINATION ${CMAKE_INSTALL_PREFIX})

cmake_minimum_required (VERSION 3.1.3)

set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the build type")

if (POLICY CMP0025)
  cmake_policy (SET CMP0025 NEW)
endif()

project (sz)
enable_testing()

find_package (OpenMP)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_EXTENSIONS OFF)

set (CMAKE_EXPORT_COMPILE_COMMANDS ON)
set (CMAKE_DISABLE_SOURCE_CHANGES ON)
set (CMAKE_DISABLE_IN_SOURCE_BUILD ON)

set (CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
set (EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set (LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

#find_package (FFTW)
if (FFTW_FOUND)
  include_directories (${FFTW_INCLUDES})
  add_definitions (-DHAVE_FFTW3)
endif ()

#find_package (netCDF)
if (netCDF_FOUND)
  include_directories (${NETCDF_INCLUDE_DIRS})
endif ()

find_program(TAGS ctags)
if(TAGS)
	add_custom_target(tags ALL
		COMMAND ${TAGS} -f ${CMAKE_BINARY_DIR}/tags -R
		COMMENT Generating Tag files
		WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
		)
endif()

find_package (ZLIB)
if (ZLIB_FOUND)
  include_directories (${ZLIB_INCLUDE_DIRS})
else ()
  set (ZLIB_LIBRARIES zlib)
  add_subdirectory (zlib)
  include_directories (${CMAKE_CURRENT_SOURCE_DIR}/zlib)
endif ()

find_package (ZSTD)
if (ZSTD_FOUND)
  include_directories (${ZSTD_INCLUDE_DIRS})
else ()
  set (ZSTD_LIBRARIES zstd)
  add_subdirectory (zstd)
  include_directories (${CMAKE_CURRENT_SOURCE_DIR}/zstd)
endif ()

include_directories (${CMAKE_CURRENT_SOURCE_DIR}/sz/include)

add_subdirectory (sz)
add_subdirectory (example)
#add_subdirectory (test)


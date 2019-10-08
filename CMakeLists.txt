cmake_minimum_required( VERSION 3.9 )

list( APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake )

project( cvmix VERSION 0.95 LANGUAGES Fortran )

### Use standard GNU installation directories
include( GNUInstallDirs )

### Configuration options
option( BUILD_STATIC_LIBS "Build static library" ON )
option( BUILD_SHARED_LIBS "Build shared library" OFF )

# Ensure at least one of BUILD_SHARED_LIBS and BUILD_STATIC_LIBS is set
if( NOT ( BUILD_STATIC_LIBS OR BUILD_SHARED_LIBS ) )
    message( STATUS "Niether BUILD_STATIC_LIBS nor BUILD_SHARED_LIBS is set.  Defaulting to BUILD_STATIC_LIBS=ON" )
    set( BUILD_STATIC_LIBS ON CACHE BOOL "Build static library" FORCE )
    set( BUILD_STATIC_LIBS ON )
endif()

message( STATUS "Option: BUILD_STATIC_LIBS: ${BUILD_STATIC_LIBS}" )
message( STATUS "Option: BUILD_SHARED_LIBS: ${BUILD_SHARED_LIBS}" )

# Set default build type to Release if not specified
if( NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE )
    message( STATUS "Setting default build type to 'Release'.  Set CMAKE_BUILD_TYPE variable to change build types." )
    set_property( CACHE CMAKE_BUILD_TYPE PROPERTY VALUE "Release" )
endif()

# Postfix for debug libraries so they can live alongside release libraries
set( CMAKE_DEBUG_POSTFIX ".debug" CACHE STRING "Debug file extension" )

## Package compiler flags
include( compiler_flags )

### Global compilation properties
set( INCLUDE_DIR ${PROJECT_NAME} ) # path relative to <prefix>/include/ to install headers/modules
set( CMAKE_Fortran_MODULE_DIRECTORY ${INCLUDE_DIR} )
set( CMAKE_POSITION_INDEPENDENT_CODE ON )

add_subdirectory(src)

# Enable ctest framework
enable_testing()

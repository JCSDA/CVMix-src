# (C) Copyright 2018 UCAR.
#
# This software is licensed under the terms of the Apache Licence Version 2.0
# which can be obtained at http://www.apache.org/licenses/LICENSE-2.0.

cmake_minimum_required( VERSION 3.3.2 FATAL_ERROR )

project( CVMix Fortran )

set( CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake;${CMAKE_MODULE_PATH} )

set( ECBUILD_DEFAULT_BUILD_TYPE Release )
set( ENABLE_OS_TESTS           OFF CACHE BOOL "Disable OS tests" FORCE )
set( ENABLE_LARGE_FILE_SUPPORT OFF CACHE BOOL "Disable testing of large file support" FORCE )

include( ecbuild_system NO_POLICY_SCOPE )

ecbuild_requires_macro_version( 2.7 )

################################################################################
# Project
################################################################################

ecbuild_declare_project()

ecbuild_enable_fortran( REQUIRED )

################################################################################
# Dependencies
################################################################################


################################################################################
# Definitions
################################################################################


################################################################################
# Export package info
################################################################################

list( APPEND CVMix_TPLS )

set( CVMix_INCLUDE_DIRS ${CMAKE_Fortran_MODULE_DIRECTORY})
set( CVMix_LIBRARIES CVMix )

get_directory_property( CVMix_DEFINITIONS COMPILE_DEFINITIONS )

foreach( _tpl ${CVMix_TPLS} )
  string( TOUPPER ${_tpl} TPL )
  list( APPEND CVMix_EXTRA_DEFINITIONS   ${${TPL}_DEFINITIONS}  ${${TPL}_TPL_DEFINITIONS}  )
  list( APPEND CVMix_EXTRA_INCLUDE_DIRS  ${${TPL}_INCLUDE_DIRS} ${${TPL}_TPL_INCLUDE_DIRS} )
  list( APPEND CVMix_EXTRA_LIBRARIES     ${${TPL}_LIBRARIES}    ${${TPL}_TPL_LIBRARIES}    )
endforeach()

################################################################################
# Sources
################################################################################

set( CVMix_LINKER_LANGUAGE Fortran )

include( CVMix_compiler_flags )
include_directories( ${CVMix_INCLUDE_DIRS} )

add_subdirectory(src)

list ( APPEND CVMix_files
	${CVMix_shared_files}
)

################################################################################
# Library
################################################################################

ecbuild_add_library( TARGET   CVMix
                     SOURCES  ${CVMix_files}
                     LINKER_LANGUAGE ${CVMix_LINKER_LANGUAGE}
                    )

################################################################################
# Finalise configuration
################################################################################

# prepares a tar.gz of the sources and/or binaries
ecbuild_install_project( NAME CVMix )

# print the summary of the configuration
ecbuild_print_summary()

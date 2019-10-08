set ( SHARED_SRCS
  cvmix_background.F90
  cvmix_convection.F90
  cvmix_ddiff.F90
  cvmix_kinds_and_types.F90
  cvmix_kpp.F90
  cvmix_math.F90
  cvmix_put_get.F90
  cvmix_shear.F90
  cvmix_tidal.F90
  cvmix_utils.F90
)

# Use a common object library for building shared and static targets
add_library( ${PROJECT_NAME}_objects OBJECT ${SHARED_SRCS} )
target_compile_definitions( ${PROJECT_NAME}_objects PUBLIC ${PUBLIC_FLAGS}
                                                    PRIVATE ${PRIVATE_FLAGS} )
target_include_directories( ${PROJECT_NAME}_objects
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${INCLUDE_DIR}> )

# Add static lib target
if( BUILD_STATIC_LIBS )
    add_library( ${PROJECT_NAME}_static STATIC $<TARGET_OBJECTS:${PROJECT_NAME}_objects> )
    list( APPEND LIB_TARGETS ${PROJECT_NAME}_static )
endif()

# Add shared lib target
if( BUILD_SHARED_LIBS )
    add_library( ${PROJECT_NAME}_shared SHARED $<TARGET_OBJECTS:${PROJECT_NAME}_objects> )
    list( APPEND LIB_TARGETS ${PROJECT_NAME}_shared )
endif()

# Set common lib target properties
set_target_properties( ${LIB_TARGETS} PROPERTIES OUTPUT_NAME ${PROJECT_NAME} )
foreach( _tgt IN LISTS LIB_TARGETS )
    target_compile_definitions( ${_tgt} PUBLIC "${PUBLIC_FLAGS}" )
    target_include_directories( ${_tgt}
        PUBLIC
            $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
            $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${INCLUDE_DIR}> )
endforeach()

### Install
install( TARGETS ${LIB_TARGETS} EXPORT ${PROJECT_NAME}Exports
         ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
         LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
         INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${INCLUDE_DIR} )
install( DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/${INCLUDE_DIR}/
         DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${INCLUDE_DIR} )

### Package config
include( CMakePackageConfigHelpers )

export( EXPORT ${PROJECT_NAME}Exports NAMESPACE ${PROJECT_NAME}:: FILE ${PROJECT_NAME}-targets.cmake )

set( CONFIG_INSTALL_DESTINATION share/${PROJECT_NAME}/cmake )

# cvmix-config.cmake
configure_package_config_file( ${PROJECT_SOURCE_DIR}/cmake/PackageConfig.cmake.in ${PROJECT_NAME}-config.cmake
                               INSTALL_DESTINATION ${CONFIG_INSTALL_DESTINATION} )
install( FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake
         DESTINATION ${CONFIG_INSTALL_DESTINATION} )

# cvmix-config-version.cmake
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion )
install( FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
         DESTINATION ${CONFIG_INSTALL_DESTINATION} )

# cvmix-targets.cmake and cvmix-targets-<build-type>.cmake
install( EXPORT ${PROJECT_NAME}Exports NAMESPACE ${PROJECT_NAME}::
         FILE ${PROJECT_NAME}-targets.cmake
         DESTINATION ${CONFIG_INSTALL_DESTINATION} )


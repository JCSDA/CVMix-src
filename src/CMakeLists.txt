add_subdirectory( shared )
add_subdirectory( drivers )

#add_executable( cvmix.x cvmix_driver.F90 cvmix_io.F90 ${DRIVERS_SRCS} )
#add_dependencies( cvmix.x ${PROJECT_NAME}_static )
#target_link_libraries( cvmix.x PUBLIC ${PROJECT_NAME}_static )
#set_target_properties( cvmix.x PROPERTIES BUILD_WITH_INSTALL_RPATH FALSE )
#set_target_properties( cvmix.x PROPERTIES SKIP_BUILD_RPATH         FALSE )
#target_include_directories( cvmix.x
#  PUBLIC $<BUILD_INTERFACE:${PROJECT_BINARY_DIR}/src/${INCLUDE_DIR}> )

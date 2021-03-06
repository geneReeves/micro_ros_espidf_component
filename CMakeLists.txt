idf_component_register(SRCS "network_interfaces/uros_ethernet_netif.c" "network_interfaces/uros_wlan_netif.c"
                       INCLUDE_DIRS "network_interfaces"
                       REQUIRES nvs_flash)

if(CMAKE_GENERATOR STREQUAL "Unix Makefiles")
set(submake "$(MAKE)")
else()
set(submake "make")
endif()

externalproject_add(libmicroros_project
    PREFIX     ${CMAKE_BINARY_DIR}/libmicroros-prefix
    SOURCE_DIR ${COMPONENT_DIR}
    BINARY_DIR ${COMPONENT_DIR}
    CONFIGURE_COMMAND ""
    BUILD_COMMAND
        ${submake} -j -f libmicroros.mk
            CC=${CMAKE_C_COMPILER}
            AR=${CMAKE_AR}
            STRIP=${CMAKE_STRIP}
            CFLAGS=${CMAKE_C_FLAGS}
            CXX=${CMAKE_CXX_COMPILER}
            CXXFLAGS=${CMAKE_CXX_FLAGS}
            BUILD_DIR=${CMAKE_BINARY_DIR}
            IDF_PATH=${IDF_PATH}
            IDF_TARGET=${IDF_TARGET}
    INSTALL_COMMAND ""
    BUILD_BYPRODUCTS ${COMPONENT_DIR}/libmicroros.a
    )

add_prebuilt_library(libmicroros-prebuilt ""
                     REQUIRES lwip)

add_dependencies(libmicroros-prebuilt libmicroros_project)

set_target_properties(libmicroros-prebuilt PROPERTIES IMPORTED_LOCATION ${COMPONENT_DIR}/libmicroros.a)
target_include_directories(libmicroros-prebuilt INTERFACE ${COMPONENT_DIR}/include)

add_dependencies(${COMPONENT_LIB} libmicroros-prebuilt)
target_link_libraries(${COMPONENT_LIB} INTERFACE libmicroros-prebuilt)

set_directory_properties( PROPERTIES ADDITIONAL_CLEAN_FILES
    "${COMPONENT_LIB}/include;${COMPONENT_LIB}/micro_ros_dev;${COMPONENT_LIB}/micro_ros_src;${COMPONENT_LIB}/esp32_toolchain.cmake" )


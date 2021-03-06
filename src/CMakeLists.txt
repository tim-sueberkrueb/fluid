include(GenerateExportHeader)
include(CMakePackageConfigHelpers)
include(ECMSetupVersion)
include(ECMGenerateHeaders)

ecm_setup_version(${PROJECT_VERSION} VARIABLE_PREFIX FLUID
                  VERSION_HEADER "${CMAKE_CURRENT_BINARY_DIR}/Fluid/fluid/fluidversion.h"
                  PACKAGE_VERSION_FILE "${CMAKE_CURRENT_BINARY_DIR}/FluidConfigVersion.cmake"
                  SOVERSION ${PROJECT_SOVERSION})

set(FLUID_INCLUDEDIR "${CMAKE_INSTALL_INCLUDEDIR}/Fluid")

set(CMAKECONFIG_INSTALL_DIR "${CMAKECONFIG_INSTALL_PREFIX}/Fluid")

configure_package_config_file("${CMAKE_CURRENT_SOURCE_DIR}/FluidConfig.cmake.in"
                              "${CMAKE_CURRENT_BINARY_DIR}/FluidConfig.cmake"
                              INSTALL_DESTINATION ${CMAKECONFIG_INSTALL_DIR})

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/FluidConfig.cmake"
              "${CMAKE_CURRENT_BINARY_DIR}/FluidConfigVersion.cmake"
        DESTINATION "${CMAKECONFIG_INSTALL_DIR}"
        COMPONENT Devel)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/Fluid/fluid/fluidversion.h
        DESTINATION ${FLUID_INCLUDEDIR}/fluid COMPONENT Devel)

###################################

add_definitions(-DQT_NO_KEYWORDS)

set(SOURCES
    dateutils.cpp
)

add_library(Fluid SHARED ${SOURCES})

target_link_libraries(Fluid
    PUBLIC
        Qt5::Core
)

##################################################

generate_export_header(Fluid EXPORT_FILE_NAME ${CMAKE_CURRENT_BINARY_DIR}/Fluid/fluid/fluid_export.h)

target_include_directories(Fluid INTERFACE "$<INSTALL_INTERFACE:${FLUID_INCLUDEDIR}>")

set_target_properties(Fluid PROPERTIES
                      VERSION ${PROJECT_VERSION}
                      SOVERSION ${PROJECT_SOVERSION})

ecm_generate_headers(Fluid_CamelCase_HEADERS
  HEADER_NAMES
    DateUtils
  REQUIRED_HEADERS
    Fluid_HEADERS
  PREFIX
    Fluid
  OUTPUT_DIR
    ${CMAKE_CURRENT_BINARY_DIR}/Fluid
)
install(FILES ${Fluid_CamelCase_HEADERS}
        DESTINATION ${FLUID_INCLUDEDIR}/Fluid
        COMPONENT Devel)

install(TARGETS Fluid EXPORT FluidTargets ${KDE_INSTALL_TARGETS_DEFAULT_ARGS})

install(
    FILES
      ${CMAKE_CURRENT_BINARY_DIR}/Fluid/fluid/fluid_export.h
      dateutils.h
    DESTINATION
      ${FLUID_INCLUDEDIR}/fluid
    COMPONENT
      Devel
)

install(EXPORT FluidTargets
        DESTINATION "${CMAKECONFIG_INSTALL_DIR}"
        FILE FluidTargets.cmake NAMESPACE Fluid::)

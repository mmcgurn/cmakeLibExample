cmake_minimum_required(VERSION 3.22)
project(MathFunctions)

# make cache variables for install destinations
include(GNUInstallDirs)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# create library
add_library(MathFunctions STATIC)
add_subdirectory(subDir)

target_sources(MathFunctions
        PRIVATE
          MathFunctions.cxx
        PUBLIC
          MathFunctions.h
        )


# add include directories
target_include_directories(MathFunctions
                           PUBLIC
                           "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
                           "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)

# install the target and create export-set
install(TARGETS MathFunctions
        EXPORT MathFunctionsTargets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# install header file(s)
install(FILES MathFunctions.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/subDir DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} FILES_MATCHING PATTERN "*.h")

# generate and install export file
install(EXPORT MathFunctionsTargets
        FILE MathFunctionsTargets.cmake
        NAMESPACE MathFunctions::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/MathFunctions
)

# include CMakePackageConfigHelpers macro
include(CMakePackageConfigHelpers)

# set version
set(version 3.4.1)

set_property(TARGET MathFunctions PROPERTY VERSION ${version})
set_property(TARGET MathFunctions PROPERTY SOVERSION 3)
set_property(TARGET MathFunctions PROPERTY
  INTERFACE_MathFunctions_MAJOR_VERSION 3)
set_property(TARGET MathFunctions APPEND PROPERTY
  COMPATIBLE_INTERFACE_STRING MathFunctions_MAJOR_VERSION
)

# generate the version file for the config file
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake"
  VERSION "${version}"
  COMPATIBILITY AnyNewerVersion
)

# create config file
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/MathFunctions
)

# install config files
install(FILES
          "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfig.cmake"
          "${CMAKE_CURRENT_BINARY_DIR}/MathFunctionsConfigVersion.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/MathFunctions
)

# generate the export targets for the build tree
export(EXPORT MathFunctionsTargets
       FILE "${CMAKE_CURRENT_BINARY_DIR}/cmake/MathFunctionsTargets.cmake"
       NAMESPACE MathFunctions::
)

# march over each target property
get_target_property(InterfaceListOrg MathFunctions INTERFACE_SOURCES)
foreach(interfaceOrg ${InterfaceListOrg})
    # Replace this hard code value with relative values
    file(RELATIVE_PATH relativeHeaderPath ${PROJECT_SOURCE_DIR} ${interfaceOrg})
    # add the lists back
    list(APPEND InterfaceListNew "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/${relativeHeaderPath}>")
    list(APPEND InterfaceListNew "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${relativeHeaderPath}>")
endforeach()
set_property(TARGET MathFunctions PROPERTY INTERFACE_SOURCES ${InterfaceListNew})


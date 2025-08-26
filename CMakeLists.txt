cmake_minimum_required(VERSION 3.24)

project(factorize
    VERSION 0.1.0
    DESCRIPTION "Create factors from categorical variables"
    LANGUAGES CXX)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# Library
add_library(factorize INTERFACE)
add_library(ltla::factorize ALIAS factorize)

target_include_directories(factorize INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/factorize>)
target_compile_features(factorize INTERFACE cxx_std_17)

# Dependencies
option(FACTORIZE_FETCH_EXTERN "Automatically fetch factorize's external dependencies." ON)
if(FACTORIZE_FETCH_EXTERN)
    add_subdirectory(extern)
else()
    find_package(ltla_sanisizer 0.1.2 CONFIG REQUIRED)
endif()

target_link_libraries(factorize INTERFACE ltla::sanisizer)

# Tests
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    option(FACTORIZE_TESTS "Build factorize's test suite." ON)
else()
    option(FACTORIZE_TESTS "Build factorize's test suite." OFF)
endif()

if(FACTORIZE_TESTS)
    include(CTest)
    if(BUILD_TESTING)
        add_subdirectory(tests)
    endif() 
endif()

# Install
install(DIRECTORY include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/factorize)

install(TARGETS factorize
    EXPORT factorizeTargets)

install(EXPORT factorizeTargets
    FILE ltla_factorizeTargets.cmake
    NAMESPACE ltla::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ltla_factorize)

configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/Config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/ltla_factorizeConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ltla_factorize)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/ltla_factorizeConfigVersion.cmake"
    COMPATIBILITY SameMajorVersion)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/ltla_factorizeConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/ltla_factorizeConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ltla_factorize)

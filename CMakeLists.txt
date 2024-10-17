cmake_minimum_required(VERSION 3.16)
project(qamqp VERSION 0.6.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_SHARED_LIBS "Build shared libraries" ON)

find_package(Qt6 COMPONENTS Core Network REQUIRED)

add_subdirectory(src)

option(BUILD_TESTS "Build tests" OFF)
if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()

option(BUILD_TUTORIALS "Build tutorials" OFF)
if(BUILD_TUTORIALS)
    add_subdirectory(tutorials)
endif()

include(GNUInstallDirs)
install(EXPORT qamqpTargets
    FILE qamqpTargets.cmake
    NAMESPACE qamqp::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/qamqp
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/qamqpConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/qamqpConfig.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/qamqpConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/qamqp
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/qamqpConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/qamqpConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/qamqp
)
cmake_minimum_required(VERSION 3.16)
project(qamqp VERSION 0.6.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Git QUIET)

if(GIT_FOUND)
    execute_process(
        COMMAND ${GIT_EXECUTABLE} describe --long --dirty --tags
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        OUTPUT_VARIABLE GIT_VERSION
        ERROR_QUIET
        OUTPUT_STRIP_TRAILING_WHITESPACE
    )

    execute_process(
        COMMAND ${GIT_EXECUTABLE} describe --abbrev=0
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        OUTPUT_VARIABLE GIT_TAG
        ERROR_QUIET
        OUTPUT_STRIP_TRAILING_WHITESPACE
    )

    if(GIT_TAG)
        string(REGEX REPLACE "^v" "" VERSION ${GIT_TAG})
    else()
        set(VERSION ${PROJECT_VERSION})
    endif()
else()
    set(GIT_VERSION "unknown")
    set(VERSION ${PROJECT_VERSION})
endif()

add_compile_definitions(GIT_VERSION="${GIT_VERSION}")

option(BUILD_SHARED_LIBS "Build shared libraries" ON)

find_package(Qt6 COMPONENTS Core Network REQUIRED)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
add_subdirectory(src)
export(TARGETS qamqp
    NAMESPACE qamqp::
    FILE "${CMAKE_CURRENT_BINARY_DIR}/qamqpTargets.cmake"
)

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
    VERSION ${VERSION}
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

set(QAMQP_HEADERS
    qamqpauthenticator.h
    qamqpchannel.h
    qamqpclient.h
    qamqpexchange.h
    qamqpglobal.h
    qamqpmessage.h
    qamqpqueue.h
    qamqptable.h
)

set(QAMQP_SOURCES
    qamqpauthenticator.cpp
    qamqpchannel.cpp
    qamqpchannelhash.cpp
    qamqpclient.cpp
    qamqpexchange.cpp
    qamqpframe.cpp
    qamqpmessage.cpp
    qamqpqueue.cpp
    qamqptable.cpp
)

if(MSVC)
    set(BUILD_SHARED_LIBS OFF CACHE BOOL "Build static library for MSVC" FORCE)
endif()

add_library(qamqp STATIC ${QAMQP_HEADERS} ${QAMQP_SOURCES})
add_library(qamqp::qamqp ALIAS qamqp)

set_target_properties(qamqp PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
    EXPORT_NAME qamqp
    OUTPUT_NAME qamqp
    DEBUG_POSTFIX d
)

target_include_directories(qamqp
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include/qamqp>
)

target_link_libraries(qamqp
    PUBLIC
        Qt6::Core
        Qt6::Network
)

target_compile_definitions(qamqp
    PRIVATE
        QAMQP_BUILD
    PUBLIC
        $<$<NOT:$<BOOL:${BUILD_SHARED_LIBS}>>:QAMQP_STATIC>
)

set_target_properties(qamqp PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
    EXPORT_NAME qamqp
)

install(TARGETS qamqp
    EXPORT qamqpTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/qamqp
)

install(FILES ${QAMQP_HEADERS}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/qamqp
)

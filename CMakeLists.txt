cmake_minimum_required(VERSION 3.1.0 FATAL_ERROR)
PROJECT(sioclient)

set(MAJOR 1)
set(MINOR 6)
set(PATCH 0)

find_package(Boost REQUIRED COMPONENTS system date_time random) 

aux_source_directory(${CMAKE_CURRENT_LIST_DIR}/src ALL_SRC)
aux_source_directory(${CMAKE_CURRENT_LIST_DIR}/src/internal ALL_SRC)
file(GLOB ALL_HEADERS ${CMAKE_CURRENT_LIST_DIR}/src/*.h )
set(SIO_INCLUDEDIR ${CMAKE_CURRENT_LIST_DIR})

add_library(sioclient ${ALL_SRC})
target_include_directories(sioclient PRIVATE ${Boost_INCLUDE_DIRS} 
    ${CMAKE_CURRENT_LIST_DIR}/src 
    ${CMAKE_CURRENT_LIST_DIR}/lib/websocketpp 
    ${CMAKE_CURRENT_LIST_DIR}/lib/rapidjson/include
)

set_property(TARGET sioclient PROPERTY CXX_STANDARD 11)
set_property(TARGET sioclient PROPERTY CXX_STANDARD_REQUIRED ON)
target_link_libraries(sioclient PRIVATE ${Boost_LIBRARIES})
if(BUILD_SHARED_LIBS)
set_target_properties(sioclient
	PROPERTIES
		SOVERSION ${MAJOR}
		VERSION ${MAJOR}.${MINOR}.${PATCH}
	)
endif()
list(APPEND TARGET_LIBRARIES sioclient)

find_package(OpenSSL)
if(OPENSSL_FOUND)
add_library(sioclient_tls ${ALL_SRC})
target_include_directories(sioclient_tls PRIVATE ${Boost_INCLUDE_DIRS} 
    ${CMAKE_CURRENT_LIST_DIR}/src 
    ${CMAKE_CURRENT_LIST_DIR}/lib/websocketpp 
    ${CMAKE_CURRENT_LIST_DIR}/lib/rapidjson/include
    ${OPENSSL_INCLUDE_DIR}
)

set_property(TARGET sioclient_tls PROPERTY CXX_STANDARD 11)
set_property(TARGET sioclient_tls PROPERTY CXX_STANDARD_REQUIRED ON)
target_link_libraries(sioclient_tls PRIVATE ${Boost_LIBRARIES} ${OPENSSL_LIBRARIES} )
target_compile_definitions(sioclient_tls PRIVATE -DSIO_TLS)
if(BUILD_SHARED_LIBS)
set_target_properties(sioclient_tls
	PROPERTIES
		SOVERSION ${MAJOR}
		VERSION ${MAJOR}.${MINOR}.${PATCH}
	)
endif()
list(APPEND TARGET_LIBRARIES sioclient_tls)

endif()

#install(FILES ${ALL_HEADERS} 
#    DESTINATION "${CMAKE_CURRENT_LIST_DIR}/build/include"
#)

#install(TARGETS ${TARGET_LIBRARIES}
#    DESTINATION "${CMAKE_CURRENT_LIST_DIR}/build/lib/${CMAKE_BUILD_TYPE}"
#)

#install(FILES ${Boost_LIBRARIES} 
#    DESTINATION "${CMAKE_CURRENT_LIST_DIR}/build/lib/${CMAKE_BUILD_TYPE}"
#)

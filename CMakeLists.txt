cmake_minimum_required (VERSION 3.20)

project(app_proto)

include (FetchContent)
FetchContent_Declare(cmake_tool GIT_REPOSITORY https://github.com/nearmeng/cmake_tools.git GIT_TAG master)
FetchContent_MakeAvailable(cmake_tool)

fetch_content(thirdparty https://github.com/nearmeng/thirdparty.git master)
fetch_content(ss_proto_common https://github.com/nearmeng/ss_proto_common.git master)

SET(BASE_DIR ${CMAKE_CURRENT_SOURCE_DIR})
preprocess_proto(cs_proto)
preprocess_proto(db_proto)

if(MSVC)
    add_compile_options(
        $<$<CONFIG:>:/MT> #---------|
        $<$<CONFIG:Debug>:/MTd> #---|-- Statically link the runtime libraries
        $<$<CONFIG:Release>:/MT> #--|
    )
endif()

file (GLOB_RECURSE CS_SRC_FILES "cs_proto/*.cc")
file (GLOB_RECURSE DB_SRC_FILES "db_proto/*.cc")

add_library(cs_proto STATIC ${CS_SRC_FILES})
add_library(mango::cs_proto ALIAS cs_proto)
target_include_directories(cs_proto PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries (cs_proto 3rd::protobuf)

add_library(db_proto STATIC ${DB_SRC_FILES})
add_library(mango::db_proto ALIAS db_proto)
target_include_directories(db_proto PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries (db_proto 3rd::protobuf)

add_library(ss_proto INTERFACE)
add_library(mango::ss_proto ALIAS ss_proto)
target_include_directories(ss_proto INTERFACE .)
target_link_libraries(ss_proto INTERFACE mango::ss_proto_common)

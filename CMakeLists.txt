cmake_minimum_required(VERSION 3.9)
cmake_policy(SET CMP0042 NEW)
set (CMAKE_CXX_STANDARD 11)

project(cld3)

find_package(Protobuf REQUIRED)

message(STATUS "CMAKE_SOURCE_DIR=${CMAKE_SOURCE_DIR}")

include_directories(${CMAKE_JS_INC})
include_directories(${CMAKE_SOURCE_DIR}/cld3/src)
include_directories(${CMAKE_SOURCE_DIR}/cld3/build/)
include_directories(${Protobuf_INCLUDE_DIRS})

file(GLOB SOURCE_FILES "src/main.cpp")

add_library(${PROJECT_NAME} SHARED ${SOURCE_FILES} ${CMAKE_JS_SRC})

set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")

target_link_libraries(${PROJECT_NAME} ${CMAKE_JS_LIB} ${Protobuf_LITE_LIBRARIES} ${CMAKE_SOURCE_DIR}/cld3/build/libcld3.a)

# Include Node-API wrappers
execute_process(COMMAND node -p "require('node-addon-api').include"
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        OUTPUT_VARIABLE NODE_ADDON_API_DIR
        )
string(REGEX REPLACE "[\r\n\"]" "" NODE_ADDON_API_DIR ${NODE_ADDON_API_DIR})

target_include_directories(${PROJECT_NAME} PRIVATE ${NODE_ADDON_API_DIR})

# define NAPI_VERSION
add_definitions(-DNAPI_VERSION=3)

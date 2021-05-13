cmake_minimum_required(VERSION 3.15)
project(simExtLuaRemoteApiClient)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_MACOSX_RPATH 1)

find_package(Boost REQUIRED)
if(UNIX AND NOT APPLE)
    find_package(Threads REQUIRED)
endif()

if(NOT LIBPLUGIN_DIR)
    if(DEFINED ENV{COPPELIASIM_ROOT_DIR})
        set(LIBPLUGIN_DIR $ENV{COPPELIASIM_ROOT_DIR}/programming/libPlugin)
    else()
        set(LIBPLUGIN_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../libPlugin)
    endif()
endif()
list(APPEND CMAKE_MODULE_PATH ${LIBPLUGIN_DIR}/cmake)
find_package(CoppeliaSim 4.1.0.0 REQUIRED)

coppeliasim_add_plugin(
    simExtLuaRemoteApiClient
    LEGACY
    SOURCES
    simExtLuaRemoteApiClient.cpp
    remoteApiLink.cpp
    ../remoteApi/extApi.c
    ../remoteApi/extApiPlatform.c
    ${COPPELIASIM_COMMON_DIR}/scriptFunctionData.cpp
    ${COPPELIASIM_COMMON_DIR}/scriptFunctionDataItem.cpp
    ${COPPELIASIM_COMMON_DIR}/shared_memory.c
)
target_compile_definitions(simExtLuaRemoteApiClient PRIVATE NON_MATLAB_PARSING)
target_compile_definitions(simExtLuaRemoteApiClient PRIVATE MAX_EXT_API_CONNECTIONS=255)
target_include_directories(simExtLuaRemoteApiClient PRIVATE ${COPPELIASIM_INCLUDE_DIR})
target_include_directories(simExtLuaRemoteApiClient PRIVATE ../remoteApi)
if(UNIX AND NOT APPLE)
    target_link_libraries(simExtLuaRemoteApiClient Threads::Threads rt)
endif()

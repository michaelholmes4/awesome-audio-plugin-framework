cmake_minimum_required(VERSION 3.15)
project(https_curl VERSION 0.0.1 LANGUAGES C)
set(CMAKE_C_STANDARD 11)

add_executable(https_curl main.c)

set(HTTPS_CURL_INCL_DIRS
    ../../modules/curl/include
    ../../modules/curl/lib)

if(APPLE)
    set(CMAKE_OSX_DEPLOYMENT_TARGET 10.15)
    target_link_libraries(https_curl PRIVATE "-framework Security -framework CoreFoundation -lz")
elseif(WIN32)
    find_package(ZLIB)
    list(APPEND HTTPS_CURL_INCL_DIRS ${ZLIB_INCLUDE_DIR})
    target_link_libraries(https_curl PRIVATE "-lschannel -lws2_32 -lcrypt32 ${ZLIB_LIBRARIES}")
else()
    message(FATAL_ERROR "OS not supported yet")
endif()

target_include_directories(https_curl PUBLIC ${HTTPS_CURL_INCL_DIRS})

# Use this to build with curl as SCU
add_library(libcurl_scu STATIC libcurl_scu.c)
target_include_directories(libcurl_scu PUBLIC ${HTTPS_CURL_INCL_DIRS})
target_link_libraries(https_curl PRIVATE libcurl_scu)

# Use this to build with cmake (more stable, painful build process)
# include(../../cmake/IncludeCurl.txt)
# target_link_libraries(https_curl PRIVATE libcurl)
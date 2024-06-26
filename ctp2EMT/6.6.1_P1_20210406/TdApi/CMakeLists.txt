cmake_minimum_required(VERSION 2.8)

set(tdapi thosttraderapi_se_6.6.1_P1_20210406)
project(${tdapi})

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  if(${CMAKE_BUILD_TYPE} STREQUAL "Debug")
    add_definitions(-D BOOST_ALL_DYN_LINK)
  else()
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
  endif()
  add_definitions(-D ISLIB -D LIB_TRADER_API_EXPORT -D V6_6_1_P1)
  set(boost_include E:/boost_1_73_0)
  set(boost_lib E:/boost_1_73_0/stage/lib)
else()
  add_definitions(-D V6_6_1_P1)
  add_compile_options(-std=c++11 -pthread)
  set(boost_include /usr/local/include)
  set(boost_lib /usr/local/lib)
endif()

include_directories(${PROJECT_SOURCE_DIR}/.. ${PROJECT_SOURCE_DIR}/../../EMT ${boost_include})
if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  link_directories(${PROJECT_SOURCE_DIR}/../../EMT/win64 ${boost_lib})
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  link_directories(${PROJECT_SOURCE_DIR}/../../EMT/linux ${boost_lib})
endif()

add_library(${tdapi} SHARED ../../FtdcTraderApiImpl.cpp)
if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  target_link_libraries(${tdapi} emt_api.lib)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  target_link_libraries(${tdapi} -lemt_api)
endif()
set_target_properties(${tdapi} PROPERTIES OUTPUT_NAME "thosttraderapi_se")
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR})

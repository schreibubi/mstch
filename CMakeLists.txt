cmake_minimum_required(VERSION 3.0.2)
project(mstch)

option(WITH_UNIT_TESTS "enable building unit test executable" OFF)
option(WITH_BENCHMARK "enable building benchmark executable" OFF)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE ON)

set (PACKAGES_DIR ${CMAKE_CURRENT_BINARY_DIR}/packages)

message(STATUS "Fetching NuGet packages...")
execute_process(WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR} COMMAND nuget.exe restore ${CMAKE_CURRENT_SOURCE_DIR}/packages.config -PackagesDirectory ${PACKAGES_DIR} RESULT_VARIABLE NUGET_RETURN_VALUE )   # -ConfigFile

IF(NUGET_RETURN_VALUE EQUAL 0)
message(STATUS "done")
ELSE()
message(FATAL_ERROR "Required package not found!")
ENDIF()

set (BOOST_ROOT    "${PACKAGES_DIR}/boost-intel.1.59.0.7/"  )
set(Boost_USE_STATIC_LIBS        OFF)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME    OFF)

add_definitions(-DBOOST_ALL_DYN_LINK)

set(mstch_VERSION 1.0.1)

if(NOT MSVC)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -O3")
endif()

add_subdirectory(src)

if(WITH_UNIT_TESTS)
  enable_testing()
  add_subdirectory(vendor/headerize)
  add_subdirectory(test)
endif()

if(WITH_BENCHMARK)
  add_subdirectory(vendor/benchmark)
  add_subdirectory(benchmark)
endif()

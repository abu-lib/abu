cmake_minimum_required(VERSION 3.16)
project(abu VERSION 0.1.0)


SET(ABU_COVERAGE OFF CACHE BOOL "Creates coverage report")
SET(ABU_BENCHMARKS OFF CACHE BOOL "Benchmark abu libraries")
SET(ABU_REPO_PREFIX https://github.com/abu-lib/ CACHE STRING "")

get_directory_property(hasParent PARENT_DIRECTORY)
if(hasParent)
  SET(ABU_BUILD_ALL_TESTS OFF CACHE BOOL "")
else()
  set(ABU_BUILD_ALL_TESTS ON CACHE BOOL "")
endif()


add_subdirectory(libs/debug)
add_subdirectory(libs/mem)

if(ABU_BUILD_ALL_TESTS)
  enable_testing()

  add_custom_target(abu_tests)
  add_dependencies(abu_tests abu_debug_tests abu_mem_tests)
endif()

if(ABU_BENCHMARKS)
  add_custom_target(abu_benchmarks)
  add_dependencies(abu_benchmarks abu_mem_benchmarks)
endif()
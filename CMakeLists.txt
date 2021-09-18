cmake_minimum_required(VERSION 3.16)
project(abu VERSION 0.1.0)

SET(ABU_META_PROJECT ON)

if(NOT DEFINED abu_master_project)
    if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
        message(STATUS "abu built as master project")
        set(abu_master_project ON)
    else()
        set(abu_master_project OFF)
    endif()
endif()

SET(ABU_TEST_ALL ${abu_master_project} CACHE BOOL "Test all abu libraries")
SET(ABU_COVERAGE OFF CACHE BOOL "Creates coverage report from tests")
SET(ABU_BENCHMARKS OFF CACHE BOOL "Benchmark abu libraries")
SET(ABU_INSTALL ${abu_master_project} CACHE BOOL "Create installation targets")

include(GNUInstallDirs)
SET(ABU_INSTALL_BINDIR ${CMAKE_INSTALL_BINDIR}/abu)
SET(ABU_INSTALL_LIBDIR ${CMAKE_INSTALL_LIBDIR}/abu)

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



set(project_config_in "${CMAKE_CURRENT_LIST_DIR}/cmake/Config.cmake.in")
set(project_config_out "${CMAKE_CURRENT_BINARY_DIR}/abuConfig.cmake")
set(config_targets_file "abuTargets.cmake")
set(config_checked_targets_file "abuCheckedTargets.cmake")
set(version_config_file "${CMAKE_CURRENT_BINARY_DIR}/abuVersion.cmake")
set(export_dest_dir "${CMAKE_INSTALL_LIBDIR}/abu/cmake")


# install(EXPORT ${PROJECT_NAME} DESTINATION ${export_dest_dir} NAMESPACE abu:: FILE ${config_targets_file})
include(CMakePackageConfigHelpers)
configure_package_config_file("${project_config_in}" "${project_config_out}"
  INSTALL_DESTINATION ${export_dest_dir})

write_basic_package_version_file("${version_config_file}" COMPATIBILITY SameMajorVersion)
install(FILES "${project_config_out}" "${version_config_file}" DESTINATION "${export_dest_dir}")

install(EXPORT abu DESTINATION ${export_dest_dir} FILE ${config_targets_file})
#install(EXPORT abu_checked DESTINATION ${export_dest_dir} FILE ${config_checked_targets_file})
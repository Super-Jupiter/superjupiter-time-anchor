cmake_minimum_required(VERSION 3.16)
project(SuperJupiterTimeAnchor VERSION 0.1.1 LANGUAGES CXX)

set(_superjupiter_time_anchor_top_level OFF)
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(_superjupiter_time_anchor_top_level ON)
endif()
option(SUPERJUPITER_TIME_ANCHOR_BUILD_TESTS "Build TimeAnchor tests" ${_superjupiter_time_anchor_top_level})
option(SUPERJUPITER_TIME_ANCHOR_BUILD_EXAMPLES "Build TimeAnchor host example" ${_superjupiter_time_anchor_top_level})
option(SUPERJUPITER_TIME_ANCHOR_INSTALL "Generate TimeAnchor install rules" ${_superjupiter_time_anchor_top_level})

add_library(SuperJupiterTimeAnchor INTERFACE)
add_library(SuperJupiter::TimeAnchor ALIAS SuperJupiterTimeAnchor)
set_target_properties(SuperJupiterTimeAnchor PROPERTIES EXPORT_NAME TimeAnchor)
target_include_directories(SuperJupiterTimeAnchor INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include>)
target_compile_features(SuperJupiterTimeAnchor INTERFACE cxx_std_11)

if(SUPERJUPITER_TIME_ANCHOR_BUILD_TESTS)
  enable_testing()
  add_executable(superjupiter-time-anchor-review tests/review_regressions.cpp)
  target_link_libraries(superjupiter-time-anchor-review PRIVATE SuperJupiter::TimeAnchor)
  add_test(NAME superjupiter-time-anchor-review COMMAND superjupiter-time-anchor-review)
  add_executable(superjupiter-time-anchor-tests tests/test.cpp)
  target_link_libraries(superjupiter-time-anchor-tests PRIVATE SuperJupiter::TimeAnchor)
  add_test(NAME superjupiter-time-anchor-tests COMMAND superjupiter-time-anchor-tests)
  add_executable(superjupiter-time-anchor-namespace-test tests/namespace_isolation.cpp)
  target_link_libraries(superjupiter-time-anchor-namespace-test PRIVATE SuperJupiter::TimeAnchor)
  add_test(NAME superjupiter-time-anchor-namespace-test COMMAND superjupiter-time-anchor-namespace-test)
endif()
if(SUPERJUPITER_TIME_ANCHOR_BUILD_EXAMPLES)
  add_executable(superjupiter-time-anchor-replay tools/replay.cpp)
  target_link_libraries(superjupiter-time-anchor-replay PRIVATE SuperJupiter::TimeAnchor)
endif()
if(SUPERJUPITER_TIME_ANCHOR_INSTALL)
  include(CMakePackageConfigHelpers)
  set(_superjupiter_time_anchor_package_dir lib/cmake/SuperJupiterTimeAnchor)
  install(TARGETS SuperJupiterTimeAnchor EXPORT SuperJupiterTimeAnchorTargets)
  install(FILES src/SuperJupiterTimeAnchor.h DESTINATION include)
  install(EXPORT SuperJupiterTimeAnchorTargets NAMESPACE SuperJupiter::
    DESTINATION ${_superjupiter_time_anchor_package_dir})
  configure_package_config_file(cmake/SuperJupiterTimeAnchorConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTimeAnchorConfig.cmake
    INSTALL_DESTINATION ${_superjupiter_time_anchor_package_dir})
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTimeAnchorConfigVersion.cmake
    VERSION ${PROJECT_VERSION} COMPATIBILITY SameMinorVersion ARCH_INDEPENDENT)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTimeAnchorConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTimeAnchorConfigVersion.cmake
    DESTINATION ${_superjupiter_time_anchor_package_dir})
  install(FILES LICENSE DESTINATION share/licenses/SuperJupiterTimeAnchor)
endif()

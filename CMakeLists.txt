cmake_minimum_required(VERSION 3.5)
project(acado_vendor)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake_auto REQUIRED)

list(APPEND extra_cmake_args -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})

if(DEFINED CMAKE_TOOLCHAIN_FILE)
  list(APPEND extra_cmake_args "-DCMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE}")
endif()

message("extra_cmake_args: ${extra_cmake_args} ====")

include(ExternalProject)

ExternalProject_Add(acado-1.2.2-beta
  GIT_REPOSITORY https://github.com/acado/acado.git
  GIT_TAG 5adb8cbcff5a5464706a48eaf073218ac87c9044
  GIT_SHALLOW 1
  TIMEOUT 600
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/acado_external_project_install
    -DCMAKE_C_FLAGS="-w"
    -DCMAKE_CXX_FLAGS="-w"
    ${extra_cmake_args}
    -Wno-dev
)
install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/acado_external_project_install/
  DESTINATION ${CMAKE_INSTALL_PREFIX})

ExternalProject_Add(hpmpc-cho3
  GIT_REPOSITORY https://github.com/gaif/hpmpc.git
  GIT_TAG 3cd096f4fa2ad19995ccc1d3930307f0d372e416
  GIT_SHALLOW 1
  TIMEOUT 600
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/hpmpc_external_project_install
    -DCMAKE_C_FLAGS="-w"
    -DCMAKE_CXX_FLAGS="-w"
    ${extra_cmake_args}
    -Wno-dev
)
install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/hpmpc_external_project_install/
  DESTINATION ${CMAKE_INSTALL_PREFIX})

ament_export_include_directories(include)
ament_export_libraries(
  acado_toolkit_s # For code generation
  hpmpc
)

# Install cmake helper functions
install(
  DIRECTORY cmake
  DESTINATION share/${PROJECT_NAME}
)

ament_auto_package(
  CONFIG_EXTRAS "acado_vendor-extras.cmake"
)

# Minimum version for cmake compatiblity
cmake_minimum_required(VERSION 3.27)

include(CMakePrintHelpers)
include(FetchContent)

# Set branch names for the sub modules dependencies
set(GITHUB_BRANCH_TOOLCHAIN "HEAD" CACHE STRING "git SHA for CMake Toolchain")
# set the location of all fetched repos
set(FETCHCONTENT_BASE_DIR "${CMAKE_SOURCE_DIR}/_deps")

# show limited messages while cloning git repos
set(FETCHCONTENT_QUIET ON)

cmake_print_variables(GITHUB_BRANCH_TOOLCHAIN)
FetchContent_Declare(
    cmake-toolchains                             # Recommendation: Stick close to the original name.
    GIT_REPOSITORY  git@github.com:kodezine/cmake-toolchains.git
    GIT_TAG         ${GITHUB_BRANCH_TOOLCHAIN}
)

# pre-fetch the toolchain repository as the first job before project configuration
FetchContent_GetProperties(cmake-toolchains)

if(NOT cmake-toolchains_POPULATED)
    FetchContent_Populate(cmake-toolchains)
endif()

set(GITHUB_BRANCH_cmsis-v5 "5.9.0")
set(PRECOMPILED_TAG_cmsis-v5 "0.1.2")
set(GITHUB_BRANCH_cubemx "1.11.1")
project(
    cubemx
    VERSION ${GITHUB_BRANCH_cubemx}
    LANGUAGES   C ASM CXX
    DESCRIPTION "Hardware Abstraction Layer for ${STM32_DEVICE}"
)

include(${cmake-toolchains_SOURCE_DIR}/frameworks/arm_cmsis-v5/arm_cmsis-v5.cmake)
include(${cmake-toolchains_SOURCE_DIR}/silicon/st/cubemx.cmake)
# This will set the CPACK tar file as
# cubemx-<cubemxVersion>-<stm32Device>-<compiler>-<compilerVersion>.tar.gz
set(CPACK_PACKAGE_CHECKSUM SHA3_256)
set(CPACK_SYSTEM_NAME "${STM32_DEVICE}-${CMAKE_C_COMPILER_ID}-${CMAKE_C_COMPILER_VERSION}")
set(CPACK_BINARY_TGZ "ON")
set(CPACK_BINARY_ZIP "OFF")
set(CPACK_BINARY_ZIP "OFF")
set(CPACK_BINARY_NSIS "OFF")
set(CPACK_SOURCE_IGNORE_FILES
  \\.git/
  build/
  ".*~$"
)
set(CPACK_VERBATIM_VARIABLES YES)
include(CPack)

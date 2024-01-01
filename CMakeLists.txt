# Minimum version for cmake compatiblity
cmake_minimum_required(VERSION 3.27)

include(CMakePrintHelpers)
include(FetchContent)

# Set branch names for the sub modules dependencies
set(GITHUB_BRANCH_cmake_scripts "HEAD" CACHE STRING "git SHA for CMake Toolchain")
# set the location of all fetched repos
set(FETCHCONTENT_BASE_DIR "${CMAKE_SOURCE_DIR}/_deps")

# show limited messages while cloning git repos
set(FETCHCONTENT_QUIET ON)

cmake_print_variables(GITHUB_BRANCH_cmake_scripts)
FetchContent_Declare(
    cmake_scripts                             # Recommendation: Stick close to the original name.
    GIT_REPOSITORY  git@github.com:kodezine/cmake_scripts.git
    GIT_TAG         ${GITHUB_BRANCH_cmake_scripts}
)

# pre-fetch the toolchain repository as the first job before project configuration
FetchContent_MakeAvailable(cmake_scripts)

set(GITHUB_BRANCH_cmsis_v5 "5.9.0")
# set(PRECOMPILED_TAG_cmsis_v5 "0.2.0")
set(GITHUB_BRANCH_cubemx "1.11.1")
set(GITHUB_BRANCH_cmsis_dsp "1.15.0")

project(
    cubemx
    VERSION ${GITHUB_BRANCH_cubemx}
    LANGUAGES   C ASM CXX
    DESCRIPTION "Hardware Abstraction Layer for ${STM32_DEVICE}"
)

include(${cmake_scripts_SOURCE_DIR}/frameworks/cmsis_v5/cmsis_v5.cmake)
include(${cmake_scripts_SOURCE_DIR}/frameworks/cmsis_dsp/cmsis_dsp.cmake)
include(${cmake_scripts_SOURCE_DIR}/silicon/st/cubemx.cmake)
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

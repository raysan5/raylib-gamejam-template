cmake_minimum_required(VERSION 3.19...3.24)

# Generate compile_commands.json
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Not ideal to use this global variable, but necessary to make sure that tooling and projects use the same version
#set(CMAKE_CXX_STANDARD 11)
set(CMAKE_C_STANDARD 99)

# strongly encouraged to enable this globally to avoid conflicts between -Wpedantic being enabled and -std=c++20 and -std=gnu++20 for
# example when compiling with PCH enabled
#set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_C_EXTENSIONS ON)

# Set the project name to your project name, my project isn't very descriptive
project(raylib_game
        VERSION 0.1.0
        LANGUAGES C CXX)

# ---- Add dependencies via CPM ----
## https://github.com/cpm-cmake/CPM.cmake
set(CPM_DOWNLOAD_VERSION 0.40.2)
if(CPM_SOURCE_CACHE)
    # Expand relative path. This is important if the provided path contains a tilde (~)
    get_filename_component(CPM_SOURCE_CACHE ${CPM_SOURCE_CACHE} ABSOLUTE)
    set(CPM_DOWNLOAD_LOCATION "${CPM_SOURCE_CACHE}/cpm/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
elseif(DEFINED ENV{CPM_SOURCE_CACHE})
    set(CPM_DOWNLOAD_LOCATION "$ENV{CPM_SOURCE_CACHE}/cpm/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
else()
    set(CPM_DOWNLOAD_LOCATION "${CMAKE_BINARY_DIR}/cmake/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
endif()
if(NOT (EXISTS ${CPM_DOWNLOAD_LOCATION}))
    message(STATUS "Downloading CPM.cmake to ${CPM_DOWNLOAD_LOCATION}")
    file(DOWNLOAD https://github.com/cpm-cmake/CPM.cmake/releases/download/v${CPM_DOWNLOAD_VERSION}/CPM.cmake ${CPM_DOWNLOAD_LOCATION})
endif()
include(${CPM_DOWNLOAD_LOCATION})
## add raylib (3rd-party) https://github.com/raysan5/raylib
cpmaddpackage(
        NAME
        raylib
        GITHUB_REPOSITORY
        raysan5/raylib
        GIT_TAG
        #5.5
        master # use up-to-date branch
)
target_compile_options(raylib PRIVATE $<$<C_COMPILER_ID:GNU,Clang>:-Wno-error=implicit-function-declaration>)
target_compile_options(raylib PRIVATE $<$<C_COMPILER_ID:GNU,Clang>:-Wno-unused-result>)
target_compile_options(raylib PRIVATE $<$<C_COMPILER_ID:GNU>:-Wno-stringop-overflow>)
target_compile_options(raylib PRIVATE $<$<C_COMPILER_ID:Clang>:-Wno-implicit-const-int-float-conversion>)
target_compile_features(raylib PRIVATE c_std_99)
if("${PLATFORM}" STREQUAL "Desktop")
    target_compile_features(glfw PRIVATE c_std_99)
endif()

# ##########################################################################################################################################
# Project
# ##########################################################################################################################################

add_subdirectory(src)

# If MSVC is being used, and ASAN is enabled, we need to set the debugger environment so that it behaves well with MSVC's debugger, and we
# can run the target from visual studio
if(MSVC)
    get_all_installable_targets(all_targets)
    message("all_targets=${all_targets}")
    set_target_properties(${all_targets} PROPERTIES VS_DEBUGGER_ENVIRONMENT "PATH=$(VC_ExecutablePath_x64);%PATH%")
endif()

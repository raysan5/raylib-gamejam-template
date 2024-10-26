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
include(cmake/PreventInSourceBuilds.cmake)

# ---- Add dependencies via CPM ----
include(cmake/CPM.cmake)
add_subdirectory(libs)

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

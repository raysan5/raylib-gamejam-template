add_executable(raylib_game)
# @NOTE: add more source files here
target_sources(raylib_game PRIVATE raylib_game.c)

target_include_directories(raylib_game PRIVATE "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>")
target_link_libraries(raylib_game raylib)
if(NOT WIN32)
    target_link_libraries(raylib_game m)
endif()

# Web Configurations
if (${PLATFORM} STREQUAL "Web")
    set_target_properties(raylib_game PROPERTIES SUFFIX ".html") # Tell Emscripten to build an example.html file.
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -s USE_GLFW=3 -s ASSERTIONS=1 -s WASM=1 -s ASYNCIFY -s GL_ENABLE_GET_PROC_ADDRESS=1")
endif()

# Checks if OSX and links appropriate frameworks (only required on MacOS)
if(APPLE)
    target_link_libraries(raylib_game "-framework IOKit")
    target_link_libraries(raylib_game "-framework Cocoa")
    target_link_libraries(raylib_game "-framework OpenGL")
endif()

# misc
set_target_properties(raylib_game PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}")
# set the startup project for the "play" button in MSVC
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT raylib_game)

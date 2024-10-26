# add raylib (3rd-party)
# https://github.com/raysan5/raylib

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
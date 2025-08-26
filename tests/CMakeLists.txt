include(FetchContent)
FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/d144031940543e15423a25ae5a8a74141044862f.zip
)
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE) # For Windows: Prevent overriding the parent project's compiler/linker settings
FetchContent_MakeAvailable(googletest)

include(GoogleTest)

add_executable(
    libtest 
    src/create_factor.cpp
    src/combine_to_factor.cpp
)

target_link_libraries(
    libtest
    factorize
    gtest_main
)

target_compile_options(libtest PRIVATE -Wall -Werror -Wpedantic -Wextra)

option(CODE_COVERAGE "Enable coverage testing" OFF)
if(CODE_COVERAGE AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(libtest PRIVATE -O0 -g --coverage)
    target_link_options(libtest PRIVATE --coverage)
endif()

gtest_discover_tests(libtest)

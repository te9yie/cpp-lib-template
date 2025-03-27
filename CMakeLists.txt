cmake_minimum_required(VERSION 3.20)
project(lib_template)


# option
option(BUILD_TESTS "Build tests" OFF)


add_library(${PROJECT_NAME} INTERFACE)
# C++17
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_17)
## UTF-8
target_compile_options(${PROJECT_NAME} INTERFACE
    $<$<CXX_COMPILER_ID:MSVC>:/utf-8>
)
## 警告レベル最大（警告をエラーとして扱う）
target_compile_options(${PROJECT_NAME} INTERFACE
    $<$<CXX_COMPILER_ID:MSVC>:/W4 /WX>
    $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -Wpedantic -Werror>
)
## 例外を無効化
target_compile_options(${PROJECT_NAME} INTERFACE
    $<$<CXX_COMPILER_ID:MSVC>:/EHa->
    $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-fno-exceptions>
)
target_compile_definitions(${PROJECT_NAME} INTERFACE
    $<$<CXX_COMPILER_ID:MSVC>:_HAS_EXCEPTIONS=0>
)
## RTTIを無効化
target_compile_options(${PROJECT_NAME} INTERFACE
    $<$<CXX_COMPILER_ID:MSVC>:/GR->
    $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-fno-rtti>
)
## include
target_include_directories(${PROJECT_NAME} INTERFACE include)


if(BUILD_TESTS)
    enable_testing()

    include(FetchContent)

    # GoogleTest
    FetchContent_Declare(
        googletest
        GIT_REPOSITORY https://github.com/google/googletest.git
        GIT_TAG v1.16.x
        GIT_SHALLOW TRUE
    )
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
    FetchContent_MakeAvailable(googletest)

    add_executable(${PROJECT_NAME}_tests
        tests/test.cpp
    )

    target_link_libraries(${PROJECT_NAME}_tests PRIVATE
        ${PROJECT_NAME}
        GTest::gtest_main
    )

    include(GoogleTest)
    gtest_discover_tests(${PROJECT_NAME}_tests)
endif()

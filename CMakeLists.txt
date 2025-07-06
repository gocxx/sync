cmake_minimum_required(VERSION 3.15)

project(gocxx_sync VERSION 0.0.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Header-only interface library
add_library(gocxx_sync INTERFACE)

target_include_directories(gocxx_sync INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

# Install headers
install(DIRECTORY include/ DESTINATION include)

# Generate .pc file
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/gocxx_sync.pc.in
    ${CMAKE_CURRENT_BINARY_DIR}/gocxx_sync.pc
    @ONLY
)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/gocxx_sync.pc
        DESTINATION lib/pkgconfig)


if(ENABLE_TESTS)
    # ---- GoogleTest via FetchContent ----
    include(FetchContent)
    FetchContent_Declare(
        googletest
        URL https://github.com/google/googletest/archive/refs/tags/v1.17.0.zip
    )
    FetchContent_MakeAvailable(googletest)

    enable_testing()

    add_executable(sync_test tests/sync_test.cpp)
    target_link_libraries(sync_test gtest_main)
    target_include_directories(sync_test PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

    include(GoogleTest)
    gtest_discover_tests(sync_test)

endif()
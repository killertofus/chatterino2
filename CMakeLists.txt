cmake_minimum_required(VERSION 3.8)

project(chatterino)

include_directories(src)

set(chatterino_SOURCES
    src/common/NetworkRequest.cpp
    src/common/NetworkResult.cpp
    src/common/NetworkPrivate.cpp
    src/common/NetworkManager.cpp
    src/common/QLogging.cpp
    src/common/Modes.cpp
    src/common/ChatterinoSetting.cpp

    src/util/DebugCount.cpp

    src/singletons/Paths.cpp

    src/BaseSettings.cpp

    src/common/UsernameSet.cpp
    src/controllers/highlights/HighlightPhrase.cpp
    )

find_package(Qt5 5.9.0 REQUIRED COMPONENTS
    Core Widgets Network Concurrent
    )

set(CMAKE_AUTOMOC ON)

# set(CMAKE_AUTOMOC ON)

if (BUILD_TESTS)
    set(BUILD_TESTS OFF)
    message("++ Tests enabled")
    find_package(GTest)
    enable_testing()

    add_executable(chatterino-test
        ${chatterino_SOURCES}

        tests/src/main.cpp
        tests/src/NetworkRequest.cpp
        tests/src/UsernameSet.cpp
        tests/src/HighlightPhrase.cpp
        )

    target_compile_definitions(chatterino-test PRIVATE CHATTERINO_GIT_HASH="test" AB_CUSTOM_SETTINGS)

    target_link_libraries(chatterino-test Qt5::Core Qt5::Widgets Qt5::Network Qt5::Concurrent)

    target_link_libraries(chatterino-test gtest gtest_main)

    add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/lib/settings)
    target_link_libraries(chatterino-test PajladaSettings)
    target_include_directories(chatterino-test PUBLIC PajladaSettings)

    find_package(Threads)

    target_link_libraries(chatterino-test Threads::Threads)

    add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/lib/serialize PajladaSerialize)

    target_link_libraries(chatterino-test PajladaSerialize)

    set_property(TARGET chatterino-test PROPERTY CXX_STANDARD 17)
    set_property(TARGET chatterino-test PROPERTY CXX_STANDARD_REQUIRED ON)

    # gtest_discover_tests(chatterino-test)
else()
    message(FATAL_ERROR "This cmake file is only intended for tests right now. Use qmake to build chatterino2")
endif()

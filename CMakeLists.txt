cmake_minimum_required(VERSION 3.18)
project(CloudSeedCore LANGUAGES CXX)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CLOUDSEED_BUFFER_SIZE 1024 CACHE STRING "Internal buffer size for audio processing")
set(CLOUDSEED_MAX_STR_SIZE 32 CACHE STRING "Maximum string length for parameter formatting")

if(MSVC)
    add_compile_options(/W3 /utf-8)
    add_compile_definitions(_CRT_SECURE_NO_WARNINGS)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

set(LIB_SOURCES
    Parameters.cpp
    DSP/Biquad.cpp
    DSP/RandomBuffer.cpp
)

set(LIB_HEADERS
    Parameters.h
    Programs.h
    DSP/AllpassDiffuser.h
    DSP/Biquad.h
    DSP/DelayLine.h
    DSP/Hp1.h
    DSP/LcgRandom.h
    DSP/Lp1.h
    DSP/ModulatedAllpass.h
    DSP/ModulatedDelay.h
    DSP/MultitapDelay.h
    DSP/RandomBuffer.h
    DSP/ReverbChannel.h
    DSP/ReverbController.h
    DSP/Utils.h
)

add_library(${PROJECT_NAME} STATIC ${LIB_SOURCES} ${LIB_HEADERS})

target_compile_definitions(${PROJECT_NAME} PUBLIC
    BUFFER_SIZE=${CLOUDSEED_BUFFER_SIZE}
    MAX_STR_SIZE=${CLOUDSEED_MAX_STR_SIZE}
)

target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

option(CLOUDSEED_BUILD_EXAMPLE "Build the example executable" OFF)

if(CLOUDSEED_BUILD_EXAMPLE)
    add_executable(${PROJECT_NAME}_example PluginProcessor.cpp)
    # TODO: this is not pretty at all
    target_compile_definitions(${PROJECT_NAME}_example PRIVATE
        BUFFER_SIZE=${CLOUDSEED_BUFFER_SIZE}
        MAX_STR_SIZE=${CLOUDSEED_MAX_STR_SIZE}
    )
    target_link_libraries(${PROJECT_NAME}_example PRIVATE ${PROJECT_NAME})
endif()

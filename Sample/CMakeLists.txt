PROJECT (AstronomicalSample)

cmake_minimum_required(VERSION 3.0)

file(GLOB AstronomicalSample_SRCS
    "*.h"
    "*.hpp"
    "*.cpp")

add_executable (AstronomicalSample ${AstronomicalSample_SRCS})

target_link_libraries (AstronomicalSample LINK_PUBLIC AALib)
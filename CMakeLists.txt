cmake_minimum_required(VERSION 3.13)

if(NOT DEFINED ${CMAKE_CUDA_ARCHITECTURES})
    set(CMAKE_CUDA_ARCHITECTURES 75 86)
endif()
message(STATUS "CUDA architectures set to ${CMAKE_CUDA_ARCHITECTURES}")

project(cuda_benchmark LANGUAGES CXX CUDA)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CUDA_STANDARD 14)

set(CMAKE_CUDA_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

find_package(CUDA REQUIRED)
add_subdirectory(external/fmt)

add_library(cuda_benchmark)
target_sources(cuda_benchmark
        PUBLIC include/cuda_benchmark.h
        PRIVATE src/cuda_benchmark.cu)
target_include_directories(cuda_benchmark
        PUBLIC include
        PRIVATE ${CUDA_INCLUDE_DIRS})
target_compile_options(cuda_benchmark PUBLIC $<$<COMPILE_LANGUAGE:CUDA>:--expt-extended-lambda>)
target_compile_features(cuda_benchmark PUBLIC cxx_std_14)
target_link_libraries(cuda_benchmark PRIVATE fmt)

add_executable(instructions_example examples/instructions.cu)
target_link_libraries(instructions_example cuda_benchmark)

add_executable(warp_latency_example examples/warp_latency.cu)
target_link_libraries(warp_latency_example cuda_benchmark)

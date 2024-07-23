cmake_minimum_required(VERSION 3.10)
PROJECT(trt_tracker VERSION 1.0 LANGUAGES C CXX CUDA)
set(CMAKE_CXX_STANDARD 11)

file(GLOB_RECURSE CPPS
        ${PROJECT_SOURCE_DIR}/lighttrack/*.cpp
        ${PROJECT_SOURCE_DIR}/lighttrack/*.cu
        ${PROJECT_SOURCE_DIR}/ostrack/*.cpp
        ${PROJECT_SOURCE_DIR}/ostrack/*.cu
        ${PROJECT_SOURCE_DIR}/trt_common/*.cpp
        ${PROJECT_SOURCE_DIR}/trt_common/*.cu
)

find_package(OpenCV REQUIRED)
find_package(CUDA REQUIRED)
find_package(Eigen3 REQUIRED)
include_directories(
        ${PROJECT_SOURCE_DIR}
        ${OpenCV_INCLUDE_DIRS}
        ${CUDA_DIR}/include
        ${TENSORRT_DIR}/include
        "/usr/include/eigen3"
)
link_directories(
        ${CUDA_DIR}/lib64
        ${TENSORRT_DIR}/lib
)
list(APPEND ALL_LIBS
        cuda cublas cudart cudnn
        nvinfer nvinfer_plugin nvonnxparser
        pthread ${OpenCV_LIBS}
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-declarations -Wfatal-errors -pthread -w")
set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11 -g -O0 -Xcompiler -fPIC")

add_library(${PROJECT_NAME} SHARED ${CPPS})
target_link_libraries(${PROJECT_NAME} ${ALL_LIBS})
target_include_directories(${PROJECT_NAME} PUBLIC 
        ${PROJECT_SOURCE_DIR}/lighttrack
        ${PROJECT_SOURCE_DIR}/ostrack
        ${PROJECT_SOURCE_DIR}/trt_common
)

# reference：https://developer.nvidia.com/cuda-gpus#compute
set_property(TARGET ${PROJECT_NAME} PROPERTY CUDA_ARCHITECTURES 89)
target_compile_options(${PROJECT_NAME} PUBLIC
        $<$<COMPILE_LANGUAGE:CUDA>:--default-stream per-thread -lineinfo --use_fast_math --disable-warnings>)
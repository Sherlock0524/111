cmake_minimum_required(VERSION 3.11)

# project name version languages
project(faster_uav_tracker_simulator VERSION 1.0.3 LANGUAGES CXX)

# set C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# set Cuda TensorRT
set(CUDA_DIR      "/usr/local/cuda")
set(TENSORRT_DIR  "/home/microlab/Documents/TensorRT-8.6.1.6")

link_directories(
        ${CUDA_DIR}/lib64
        ${TENSORRT_DIR}/lib
)

# find package
# opencv
find_package(OpenCV REQUIRED)
# opencv
find_package(Eigen3 REQUIRED)
# catkin package
find_package(catkin REQUIRED COMPONENTS
    cv_bridge
    roscpp
    roslib
    std_msgs
    mavros_msgs
    geometry_msgs
    gazebo_msgs
    px4_cmd
    tf
)
find_package(Boost REQUIRED COMPONENTS system)

# include dir
include_directories(
    include
    ${catkin_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
)

# third party lib
add_subdirectory(lib)

catkin_package(
    # INCLUDE_DIRS include
    CATKIN_DEPENDS std_msgs
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-declarations -Wfatal-errors -pthread -w")
set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11 -g -O0 -Xcompiler -fPIC")

###################  Exec  ###################
## Hover ##
# set_mode
add_executable(run src/simulator.cpp)
target_link_libraries(run trt_tracker ${catkin_LIBRARIES} Eigen3::Eigen)

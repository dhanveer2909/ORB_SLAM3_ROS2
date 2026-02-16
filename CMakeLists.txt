cmake_minimum_required(VERSION 3.5)
project(orbslam3)

# -----------------------------
# C++ standard
# -----------------------------
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# -----------------------------
# ORB-SLAM3 paths
# -----------------------------
set(ORB_SLAM3_ROOT_DIR "$ENV{HOME}/ORB_SLAM3")

set(ORB_SLAM3_INCLUDE_DIR
  ${ORB_SLAM3_ROOT_DIR}/include
)

set(ORB_SLAM3_LIBRARY
  ${ORB_SLAM3_ROOT_DIR}/lib/libORB_SLAM3.so
)

# -----------------------------
# Dependencies
# -----------------------------
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(message_filters REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Pangolin REQUIRED)
find_package(Sophus REQUIRED)

include_directories(
  include
  ${ORB_SLAM3_ROOT_DIR}
  ${ORB_SLAM3_ROOT_DIR}/include
  ${ORB_SLAM3_ROOT_DIR}/include/CameraModels
  ${ORB_SLAM3_ROOT_DIR}/Thirdparty
  ${ORB_SLAM3_ROOT_DIR}/Thirdparty/Sophus
  ${ORB_SLAM3_ROOT_DIR}/Thirdparty/DBoW2
  ${EIGEN3_INCLUDE_DIR}
  ${OpenCV_INCLUDE_DIRS}
)

# =====================================================
# MONOCULAR NODE
# =====================================================
add_executable(mono
  src/monocular/mono.cpp
  src/monocular/monocular-slam-node.cpp
)

ament_target_dependencies(mono
  rclcpp
  sensor_msgs
  cv_bridge
  message_filters
)

target_link_libraries(mono
  ${ORB_SLAM3_LIBRARY}
  ${OpenCV_LIBS}
  pangolin
)

# =====================================================
# RGBD NODE
# =====================================================
add_executable(rgbd
  src/rgbd/rgbd.cpp
  src/rgbd/rgbd-slam-node.cpp
)

ament_target_dependencies(rgbd
  rclcpp
  sensor_msgs
  cv_bridge
  message_filters
)

target_link_libraries(rgbd
  ${ORB_SLAM3_LIBRARY}
  ${OpenCV_LIBS}
  pangolin
)

# =====================================================
# STEREO NODE
# =====================================================
add_executable(stereo
  src/stereo/stereo.cpp
  src/stereo/stereo-slam-node.cpp
)

ament_target_dependencies(stereo
  rclcpp
  sensor_msgs
  cv_bridge
  message_filters
)

target_link_libraries(stereo
  ${ORB_SLAM3_LIBRARY}
  ${OpenCV_LIBS}
  pangolin
)

# =====================================================
# STEREO-INERTIAL NODE
# =====================================================
add_executable(stereo-inertial
  src/stereo-inertial/stereo-inertial.cpp
  src/stereo-inertial/stereo-inertial-node.cpp
)

ament_target_dependencies(stereo-inertial
  rclcpp
  sensor_msgs
  cv_bridge
  message_filters
)

target_link_libraries(stereo-inertial
  ${ORB_SLAM3_LIBRARY}
  ${OpenCV_LIBS}
  pangolin
)

# =====================================================
# Install
# =====================================================
install(TARGETS
  mono
  rgbd
  stereo
  stereo-inertial
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()


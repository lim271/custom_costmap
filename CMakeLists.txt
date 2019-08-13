cmake_minimum_required(VERSION 2.8.3)
project(custom_costmap)

find_package(catkin REQUIRED
  COMPONENTS
    cmake_modules
    dynamic_reconfigure
    geometry_msgs
    laser_geometry
    map_msgs
    nav_msgs
    pluginlib
    roscpp
    sensor_msgs
    tf2
    tf2_geometry_msgs
    tf2_ros
    tf2_sensor_msgs
    costmap_2d
  )

find_package(Eigen3 REQUIRED)
find_package(Boost REQUIRED COMPONENTS system thread)
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)

add_definitions(${EIGEN3_DEFINITIONS})

# dynamic reconfigure
generate_dynamic_reconfigure_options(
  cfg/ObstaclePlugin.cfg
)

catkin_package(
  INCLUDE_DIRS
    include
    ${EIGEN3_INCLUDE_DIRS}
  LIBRARIES custom_costmap
  CATKIN_DEPENDS
    dynamic_reconfigure
    geometry_msgs
    laser_geometry
    map_msgs
    message_runtime
    nav_msgs
    pluginlib
    roscpp
    sensor_msgs
    tf2_ros
    costmap_2d
  DEPENDS
    EIGEN3
    Boost
)

add_library(custom_costmap
  plugins/obstacle_layer.cpp
)
add_dependencies(custom_costmap ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(custom_costmap
  ${boost_LIBRARIES}
  ${catkin_LIBRARIES}
)


install(TARGETS
  custom_costmap
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(FILES plugins.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

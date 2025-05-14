cmake_minimum_required(VERSION 3.8)
project(my_package_library)

# Set default c++ version
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Set default c++ flags
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# Get the default GNU install directories
include(GNUInstallDirs)

# Find dependencies
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
ament_export_dependencies(rclcpp std_msgs)

include_directories(include)

add_library(my_library SHARED src/my_lib.cpp
                              include/my_package_library/my_lib.hpp)
ament_target_dependencies(my_library PUBLIC rclcpp std_msgs)
message(
  STATUS
    "INSTALL_INTERFACE:CMAKE_INSTALL_INCLUDEDIR: $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
)
message(
  STATUS
    "BUILD_INTERFACE:CMAKE_SOURCE_DIR: $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include/>"
)
target_include_directories(
  my_library PUBLIC "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
                    "$<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include/>")
ament_export_libraries(my_library)
set_target_properties(
  my_library
  PROPERTIES VERSION "0.0.0"
             SOVERSION "0.0"
             PUBLIC_HEADER "include/my_package_library/my_lib.hpp")
install(
  TARGETS my_library
  EXPORT ${PROJECT_NAME}_targets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  PUBLIC_HEADER
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}/${CURRENT_FOLDER})
ament_export_targets(${PROJECT_NAME}_targets)
ament_package()

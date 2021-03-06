cmake_minimum_required(VERSION 3.5)

project(r2m_sensors)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rmw REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(PkgConfig REQUIRED)
find_package(rosidl_default_generators REQUIRED)

pkg_check_modules(libgps REQUIRED IMPORTED_TARGET libgps)
#pkg_check_modules(libi2c REQUIRED IMPORTED_TARGET libi2c)

add_executable(r2m_epd src/r2m_epd.cpp src/io_i2c.cpp src/epd/EPD_1in54.c src/epd/GUI_BMPfile.c src/epd/GUI_Paint.c src/epd/font12.c src/epd/font12CN.c src/epd/font16.c src/epd/font20.c src/epd/font24.c src/epd/font24CN.c src/epd/font8.c )
ament_target_dependencies(r2m_epd
  "rclcpp"
  "sensor_msgs"
)

add_executable(r2m_motorctrl src/r2m_motorctrl.cpp src/io_i2c.cpp)
ament_target_dependencies(r2m_motorctrl
  "rclcpp"
  "sensor_msgs"
  "std_msgs"
  "std_srvs"
)

add_executable(r2m_laser src/r2m_laser.cpp)
ament_target_dependencies(r2m_laser
  "rclcpp"
  "sensor_msgs"
)

add_executable(r2m_gps src/r2m_gps.cpp)
ament_target_dependencies(r2m_gps
  "rclcpp"
  "sensor_msgs"
)

target_link_libraries(r2m_gps
        gps)

add_executable(r2m_powersupply src/r2m_powersupply.cpp src/io_i2c.cpp)
ament_target_dependencies(r2m_powersupply
  "rclcpp"
  "std_msgs"
  "std_srvs"
)

target_link_libraries(r2m_powersupply
        i2c)

add_executable(r2m_joint_states src/r2m_joint_states.cpp)
ament_target_dependencies(r2m_joint_states
  "rclcpp"
  "sensor_msgs")

rosidl_generate_interfaces(${PROJECT_NAME}
"srv/AddTwoInts.srv"
"srv/SetMotorPID.srv"
)

rosidl_target_interfaces(r2m_motorctrl
      ${PROJECT_NAME} "rosidl_typesupport_cpp")

install(TARGETS
  r2m_joint_states
  r2m_laser
  r2m_gps
  r2m_epd
  r2m_motorctrl
  r2m_powersupply
  DESTINATION lib/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_export_dependencies(rosidl_default_runtime)
ament_package()

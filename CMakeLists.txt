cmake_minimum_required(VERSION 3.5)
project(urg_c)

find_package(ament_cmake REQUIRED)

if(WIN32)
  message(WARNING "${PROJECT_NAME} is currently not supported on Windows")
  ament_package()
  return()
endif()

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# Set library locations to better handle if upstream locations change
set(LIBRARY_DIR ${CMAKE_CURRENT_SOURCE_DIR}/current)
set(LIBRARY_INCLUDE_DIR ${LIBRARY_DIR}/include)
set(LIBRARY_SRC_DIR ${LIBRARY_DIR}/src)
set(LIBRARY_SAMPLE_DIR ${LIBRARY_DIR}/samples)

###################################################
## Declare things to be passed to other projects ##
###################################################

###########
## Build ##
###########

## Declare libraries
add_library(urg_c SHARED
  ${LIBRARY_SRC_DIR}/urg_sensor.c
  ${LIBRARY_SRC_DIR}/urg_utils.c
  ${LIBRARY_SRC_DIR}/urg_debug.c
  ${LIBRARY_SRC_DIR}/urg_connection.c
  ${LIBRARY_SRC_DIR}/urg_ring_buffer.c
  ${LIBRARY_SRC_DIR}/urg_serial.c
  ${LIBRARY_SRC_DIR}/urg_serial_utils.c
  ${LIBRARY_SRC_DIR}/urg_tcpclient.c
  ${LIBRARY_SRC_DIR}/urg_time.c
)
target_include_directories(urg_c
  PUBLIC
  $<BUILD_INTERFACE:${LIBRARY_INCLUDE_DIR}>
  $<INSTALL_INTERFACE:include>
)
if(CMAKE_COMPILER_IS_GNUCXX)
  target_link_libraries(urg_c -lrt -lm)
endif(CMAKE_COMPILER_IS_GNUCXX)

add_library(open_urg_sensor ${LIBRARY_SAMPLE_DIR}/open_urg_sensor.c)
target_link_libraries(open_urg_sensor -lm urg_c)

## Declare samples
add_executable(angle_convert_test ${LIBRARY_SAMPLE_DIR}/angle_convert_test.c)
target_link_libraries(angle_convert_test urg_c open_urg_sensor)

add_executable(calculate_xy ${LIBRARY_SAMPLE_DIR}/calculate_xy.c)
target_link_libraries(calculate_xy urg_c open_urg_sensor)

add_executable(find_port ${LIBRARY_SAMPLE_DIR}/find_port.c)
target_link_libraries(find_port urg_c open_urg_sensor)

add_executable(get_distance ${LIBRARY_SAMPLE_DIR}/get_distance.c)
target_link_libraries(get_distance urg_c open_urg_sensor)

add_executable(get_distance_intensity ${LIBRARY_SAMPLE_DIR}/get_distance_intensity.c)
target_link_libraries(get_distance_intensity urg_c open_urg_sensor)

add_executable(get_multiecho ${LIBRARY_SAMPLE_DIR}/get_multiecho.c)
target_link_libraries(get_multiecho urg_c open_urg_sensor)

add_executable(get_multiecho_intensity ${LIBRARY_SAMPLE_DIR}/get_multiecho_intensity.c)
target_link_libraries(get_multiecho_intensity urg_c open_urg_sensor)

add_executable(reboot_test ${LIBRARY_SAMPLE_DIR}/reboot_test.c)
target_link_libraries(reboot_test urg_c open_urg_sensor)

add_executable(sensor_parameter ${LIBRARY_SAMPLE_DIR}/sensor_parameter.c)
target_link_libraries(sensor_parameter urg_c open_urg_sensor)

add_executable(sync_time_stamp ${LIBRARY_SAMPLE_DIR}/sync_time_stamp.c)
target_link_libraries(sync_time_stamp urg_c open_urg_sensor)

add_executable(timeout_test ${LIBRARY_SAMPLE_DIR}/timeout_test.c)
target_link_libraries(timeout_test urg_c open_urg_sensor)

#############
## Install ##
#############

## Mark executables and/or libraries for installation
install(TARGETS urg_c open_urg_sensor angle_convert_test calculate_xy find_port
  get_distance get_distance_intensity get_multiecho get_multiecho_intensity
  reboot_test sensor_parameter sync_time_stamp timeout_test
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

## Mark cpp header files for installation
install(DIRECTORY ${LIBRARY_INCLUDE_DIR}/
  DESTINATION include
)

ament_export_include_directories(include)
ament_export_libraries(urg_c open_urg_sensor)
ament_package()

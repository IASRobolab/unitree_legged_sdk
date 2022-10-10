cmake_minimum_required(VERSION 2.8.3)
project(unitree_legged_sdk)

add_compile_options(-std=c++14)

# check arch and os
message("-- CMAKE_SYSTEM_PROCESSOR: ${CMAKE_SYSTEM_PROCESSOR}")
if("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "x86_64.*")
  set(ARCH amd64)
endif()
if("${CMAKE_SYSTEM_PROCESSOR}" MATCHES "aarch64.*")
  set(ARCH arm64)
endif()

include_directories(include)
link_directories(lib/cpp/${ARCH})
set(EXTRA_LIBS -pthread libunitree_legged_sdk.a)

set(CMAKE_CXX_FLAGS "-O3 -fPIC")

find_package(catkin REQUIRED)

catkin_package(
  LIBRARIES unitree_legged_sdk
  INCLUDE_DIRS include
  )

# Library
find_library(UNITREE_LEGGED_SDK_LIB libunitree_legged_sdk.a PATHS lib/cpp/${ARCH}/ NO_DEFAULT_PATH)
add_library(unitree_legged_sdk ${UNITREE_LEGGED_SDK_LIB})
set_target_properties(unitree_legged_sdk PROPERTIES LINKER_LANGUAGE CXX)

# Examples
add_executable(example_position example/example_position.cpp)
target_link_libraries(example_position ${EXTRA_LIBS})

add_executable(example_velocity example/example_velocity.cpp)
target_link_libraries(example_velocity ${EXTRA_LIBS})

add_executable(example_torque example/example_torque.cpp)
target_link_libraries(example_torque ${EXTRA_LIBS})

add_executable(example_walk example/example_walk.cpp)
target_link_libraries(example_walk ${EXTRA_LIBS})

add_executable(example_joystick example/example_joystick.cpp)
target_link_libraries(example_joystick ${EXTRA_LIBS})

# Install
install(TARGETS example_position example_velocity example_torque example_walk example_joystick unitree_legged_sdk
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

#install(DIRECTORY lib/cpp/${ARCH}/
#  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

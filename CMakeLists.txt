cmake_minimum_required(VERSION 3.5)

project(example_interfaces)

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rosidl_default_generators REQUIRED)

set(EXCEPTION_MSG 
# msg here won't be built.
)

macro(subdirlist result dir type)
  file(GLOB children "${dir}/${type}")
  set(filelist "")
  foreach(child ${children})
      get_filename_component(child ${child} NAME)
      list(FIND EXCEPTION_MSG ${child} index)
      if(${index} EQUAL -1)
        list(APPEND filelist "${dir}/${child}")
      else()
        message("${child} is not supported in ros2")
      endif()
  endforeach()
  set(${result} ${filelist})
endmacro()

subdirlist(MSG_FILES "msg" "*.msg")
subdirlist(SRV_FILES "srv" "*.srv")
subdirlist(ACT_FILES "action" "*.action")

rosidl_generate_interfaces(${PROJECT_NAME}
  ${MSG_FILES}
  ${SRV_FILES}
  ${ACT_FILES}
)

install(FILES mapping_rules.yaml DESTINATION share/${PROJECT_NAME})

ament_export_dependencies(rosidl_default_runtime)
ament_package()

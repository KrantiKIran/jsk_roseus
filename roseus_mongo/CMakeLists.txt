if ("$ENV{ROS_DISTRO}" STRGREATER "indigo")
  return()
endif()
cmake_minimum_required(VERSION 2.8.3)
project(roseus_mongo)

if ("$ENV{ROS_DISTRO}" STRLESS "jade") # up to indigo we need to compile msg fiel
  set(COMPILE_PACKAGES mongodb_store_msgs)
endif()

find_package(catkin REQUIRED COMPONENTS
  mongodb_store ${COMPILE_PACKAGES}
  roseus
)

catkin_package(
  CATKIN_DEPENDS mongodb_store_msgs roseus
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

install(DIRECTORY euslisp
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
  USE_SOURCE_PERMISSIONS)

if(CATKIN_ENABLE_TESTING)
  find_package(rostest REQUIRED)
  add_rostest(test/test_json_encode.test)
  add_rostest(test/test_json_decode.test)
  if(${mongodb_store_VERSION} VERSION_LESS "0.1.17")
    message(WARNING "detected old mongodb_store. Testing with old launch file")
    add_rostest(test/test_mongo_client_hydro.test)
  else()
    add_rostest(test/test_mongo_client.test)
  endif()
endif()

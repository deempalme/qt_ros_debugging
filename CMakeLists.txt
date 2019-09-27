project(my_carmaker_project)
cmake_minimum_required(VERSION 3.1)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Example for source files :::::::::::::::::::::::::::::::::::::::::::::
set(SOURCES
  src/CM_Main.cpp     # Required if you are using CarMaker
  src/CM_Vehicle.cpp  # Required if you are using CarMaker
  src/User.cpp        # Required if you are using CarMaker
  src/app_tmp.c       # Required if you are using CarMaker
)

# Example for ROS messages :::::::::::::::::::::::::::::::::::::::::::::
# Adding the next files (which don't have standard extensions: .cpp, .h, .c, etc.)
# would not affect the building, this is only to display the files in the
# "Project Browser" of qtcreator (left toolbar where all the porject's
# files are displayed).
set(MESSAGES
  msg/carmaker_ego_car.msg
  msg/carmaker_fspace_sensor.msg
  msg/carmaker_objects.msg
)

if(DEFINED CATKIN_DEVEL_PREFIX)
  #––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––#
  #      adding all the ROS elements inside this IF conditional      #
  #––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––––#
  ## Find catkin macros and libraries
  ## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
  ## is used, also find other catkin packages
  find_package(catkin REQUIRED COMPONENTS
    genmsg
    roscpp
    rospy
    std_msgs
    message_generation
  )

  # Example for ROS messages :::::::::::::::::::::::::::::::::::::::::::::
  # You could remove this if you are not publishing ROS messages
  ## Generate messages in the 'msg' folder
  add_message_files(FILES
     carmaker_ego_car.msg
     carmaker_fspace_sensor.msg
     carmaker_objects.msg
  )

  ## Generate added messages and services with any dependencies listed here
  generate_messages(DEPENDENCIES std_msgs)

  ###################################
  ## catkin specific configuration ##
  ###################################
  ## The catkin_package macro generates cmake config files for your package
  ## Declare things to be passed to dependent projects
  ## INCLUDE_DIRS: uncomment this if you package contains header files
  ## LIBRARIES: libraries you create in this project that dependent projects also need
  ## CATKIN_DEPENDS: catkin_packages dependent projects also need
  ## DEPENDS: system dependencies of this project that dependent projects also need
  catkin_package(
  #  INCLUDE_DIRS my_other_project/include
  #  LIBRARIES ExampleLibraryName
    CATKIN_DEPENDS message_runtime
  #  DEPENDS system_lib
  )

  # Use only if you are using CarMaker
  # Copying necessary files to run connect executable with CarMaker
  file(GLOB_RECURSE sim_input LIST_DIRECTORIES true ${PROJECT_SOURCE_DIR}/simInput/*)
  foreach(CONTENT ${sim_input})
      file(COPY ${CONTENT} DESTINATION ${CMAKE_BINARY_DIR}/3f_se_carmaker/simInput)
  endforeach(CONTENT)

  file(GLOB_RECURSE se_data LIST_DIRECTORIES true ${PROJECT_SOURCE_DIR}/Data/*)
  foreach(CONTENT ${se_data})
      file(COPY ${CONTENT} DESTINATION ${CMAKE_BINARY_DIR}/3f_se_carmaker/Data)
  endforeach(CONTENT)

endif(DEFINED CATKIN_DEVEL_PREFIX)

#carmaker directorys
set(CARMAKER_DIR /opt/ipg/hil/linux-6.0.3)
set(CARMAKER_LIB_DIR /opt/ipg/hil/linux-6.0.3/lib64)
set(CARMAKER_INC_DIR /opt/ipg/hil/linux-6.0.3/include)

#ros directory
set(ROS_INC_DIR /opt/ros/kinetic)

#set include directories
set(INC_DIRS
  ${CARMAKER_DIR}
  ${CARMAKER_INC_DIR}
  ${ROS_INC_DIR}/include
  ${catkin_INCLUDE_DIRS}
)

#set lib directories
set(LIB_DIRS
  ${CARMAKER_LIB_DIR}
  ${ROS_INC_DIR}/lib
)

#include directories
include_directories(${INC_DIRS})
link_directories(${LIB_DIRS})

#build settings
add_executable(CarMaker.linux64
  ${SOURCES}
  ${MESSAGES}
)

if(DEFINED CATKIN_DEVEL_PREFIX)
  add_dependencies(CarMaker.linux64
    ${${PROJECT_NAME}_EXPORTED_TARGETS}
    ${catkin_EXPORTED_TARGETS}
  )
endif(DEFINED CATKIN_DEVEL_PREFIX)

TARGET_LINK_LIBRARIES(CarMaker.linux64
  ${catkin_LIBRARIES}
  car
  carmaker
  ipgdriver
  ipgroad
  ipgtire
  tametire
  usb
  z-linux64
  dl
)

#compiler and linker flags
set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -DNDEBUG -DLINUX -DLINUX64 -D_GNU_SOURCE -D_FILE_OFFSET_BITS=64 -DCM_NUMVER=50104  -m64 -fPIC -Wall -lstdc++ -std=c++11 -pthread")
set(CMAKE_CC_FLAGS "${CMAKE_CC_FLAGS} -O3 -DNDEBUG -DLINUX -DLINUX64 -D_GNU_SOURCE -D_FILE_OFFSET_BITS=64 -DCM_NUMVER=50104 -m64 -fPIC -Wall -lstdc++ ")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -DNDEBUG -DLINUX -DLINUX64 -D_GNU_SOURCE -D_FILE_OFFSET_BITS=64 -DCM_NUMVER=50104 -m64 -fPIC -Wall -Wimplicit -Wmissing-prototypes")

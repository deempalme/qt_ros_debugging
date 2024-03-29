
cmake_minimum_required(VERSION 3.1)

project(my_catkin_qt_project
  VERSION 1.0.0
  DESCRIPTION "Example of debugging Catkin using Qt"
  LANGUAGES CXX
)

# Example for source files :::::::::::::::::::::::::::::::::::::::::::::
set(SOURCES
  src/some_file.cpp
  src/main.cpp
)

# Example for ROS messages :::::::::::::::::::::::::::::::::::::::::::::
# Adding the next files (which don't have standard extensions: .cpp, .h, .c, etc.)
# would not affect the building, this is only to display the files in the
# "Project Browser" of qtcreator (left toolbar where all the porject's
# files are displayed).
set(MESSAGES
  msg/some_message.msg
  msg/another_message.msg
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
    message_generation
    roscpp
    rospy
    std_msgs
  )

  # Example for ROS messages :::::::::::::::::::::::::::::::::::::::::::::
  # You could remove this if you are not publishing ROS messages
  ## Generate messages in the 'msg' folder
  add_message_files(FILES
     some_message.msg
     another_message.msg
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

endif(DEFINED CATKIN_DEVEL_PREFIX)

# Main ROS directory
set(ROS_INC_DIR /opt/ros/kinetic)

# Setting ROS include directories
target_include_directories(${PROJECT_NAME}
  PRIVATE
  ${ROS_INC_DIR}/include
  ${catkin_INCLUDE_DIRS}
)

# Setting ROS lib directories
target_link_directories(${PROJECT_NAME}
  BEFORE
  PRIVATE
  ${ROS_INC_DIR}/lib
)

# Creating the executable
add_executable(${PROJECT_NAME}
  ${SOURCES}
  ${MESSAGES}
)

if(DEFINED CATKIN_DEVEL_PREFIX)
  # Adding catkin and ROS dependencies
  add_dependencies(${PROJECT_NAME}
    ${PROJECT_NAME}_generate_messages # in case you are generating messages
    ${catkin_EXPORTED_TARGETS}
  )
endif(DEFINED CATKIN_DEVEL_PREFIX)

# Linking catking libraries
target_link_libraries(${PROJECT_NAME}
  ${catkin_LIBRARIES}
)

# Setting target properties; mostly C++ version
set_target_properties(${PROJECT_NAME}
  PROPERTIES
  CXX_STANDARD 14
  CXX_STANDARD_REQUIRED yes
  CXX_EXTENSIONS no
)

# Setting the C++ version
target_compile_features(${PROJECT_NAME}
  PUBLIC
  cxx_std_14
)


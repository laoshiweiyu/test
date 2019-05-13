
cmake_minimum_required(VERSION 2.8)
set(PROJECT_NAME "REVO")
project(${PROJECT_NAME})
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/" )
MESSAGE("cmake: ${CMAKE_SOURCE_DIR}/ThirdParty/libuvc/build/CMakeFiles")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -O1 -msse3 -msse4.1 -mavx2 -fopenmp")
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -O1 -msse3 -msse4.1 -mavx2 -fopenmp")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -msse3 -msse4.1 -mavx2 -fopenmp")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -msse3 -msse4.1 -mavx2 -fopenmp")
SET(CMAKE_BUILD_TYPE DEBUG)
#SET(CMAKE_BUILD_TYPE RELEASE)
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
     message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has C++11 support. Please use a different C++ compiler.")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
 	message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

#thirdparty

OPTION(WITH_PANGOLIN_VIEWER "Build with Pangolin viewer?" ON)
if (WITH_PANGOLIN_VIEWER)
    find_package(Pangolin REQUIRED)
    include_directories(${Pangolin_INCLUDE_DIRS})
    list(APPEND LIBS ${Pangolin_LIBRARIES})
    list(APPEND SRC_FILES "gui/Viewer.cc" "gui/MapDrawer.cc")
    list(APPEND HEADER_FILES "gui/Viewer.h" "gui/MapDrawer.h" "gui/PointCloudBuffered.h")
    add_definitions(-DWITH_PANGOLIN_VIEWER)
endif()

#LibRealSense


find_package(OpenCV 3 REQUIRED opencv_core opencv_videoio opencv_imgcodecs opencv_imgproc opencv_calib3d opencv_highgui)
if(NOT OpenCV_FOUND)
   find_package(OpenCV 2.4.3 QUIET)
   if(NOT OpenCV_FOUND)
      message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
   endif()
endif()

#Orbbec Astra




#for the standard Orbbec Astra


list(APPEND LIBS ${OpenCV_LIBS})
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${FLAGS}")
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} ${FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${FLAGS}")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${FLAGS}")
message("OpenCV: ${OpenCV_INCLUDE_DIRS}")
find_package(Eigen3 REQUIRED)
INCLUDE_DIRECTORIES("thirdparty/Sophus")
include_directories(${EIGEN3_INCLUDE_DIRS})
message("Eigen found: ${EIGEN3_INCLUDE_DIRS} ${EIGEN_INCLUDE_DIRS} ${Eigen_INCLUDE_DIRS}")




find_package(Boost COMPONENTS system filesystem REQUIRED)
list(APPEND LIBS ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY})

#UTILS
list(APPEND UTILITY_SRC_FILES "utils/Logging.cpp")
list(APPEND UTILITY_HEADER_FILES "utils/timer.h" "utils/Logging.h")

#DATA STRUCTURES
list(APPEND DATASTRUC_SRC_FILES "datastructures/imgpyramidrgbd.cpp")
list(APPEND DATASTRUC_HEADER_FILES "datastructures/imgpyramidrgbd.h" "datastructures/camerapyr.h")

#Input/Output
list(APPEND DATASTRUC_SRC_FILES "io/iowrapperRGBD.cpp")
list(APPEND DATASTRUC_HEADER_FILES "io/iowrapperRGBD.h")

#CORE SYSTEM
list(APPEND DATASTRUC_SRC_FILES "system/system.cpp" "system/tracker.cpp" "system/optimizer.cpp")# "system/windowedoptimization.cpp")
list(APPEND DATASTRUC_HEADER_FILES "system/system.h" "system/tracker.h" "system/optimizer.h")# "system/windowedoptimization.h")

list(APPEND SRC_FILES ${SRC_FILES} "main.cpp")
add_executable(${PROJECT_NAME} ${SRC_FILES} ${HEADER_FILES} ${UTILITY_SRC_FILES} ${UTILITY_HEADER_FILES} ${DATASTRUC_SRC_FILES} ${DATASTRUC_HEADER_FILES})
#message("libuvc_LIBRARIES: ${libuvc_LIBRARIES}, ${LIBS}")
target_link_libraries(${PROJECT_NAME} ${LIBS} ${FFMPEG_LIBRARIES})
cmake_minimum_required(VERSION 2.8.3)
project(aruco)
# project(aruco VERSION "3.1.11" LANGUAGES CXX)

find_package(catkin)
find_package(Eigen3 REQUIRED)
find_package(OpenCV 3 REQUIRED)
set(CMAKE_CXX_STANDARD 11) # C++11...
set(CMAKE_CXX_STANDARD_REQUIRED ON) #...is required...
set(CMAKE_CXX_EXTENSIONS ON) #...with compiler extensions like gnu++11

catkin_package(
  INCLUDE_DIRS include include/aruco include/aruco/fractallabelers
  LIBRARIES aruco
)

include_directories(
  include/aruco/
  include/aruco/fractallabelers
)

include_directories(
  SYSTEM
  ${catkin_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
)

SET(sources
  src/aruco/cameraparameters.cpp
  src/aruco/cvdrawingutils.cpp
  src/aruco/debug.cpp
  src/aruco/dictionary.cpp
  src/aruco/dictionary_based.cpp
  src/aruco/fractaldetector.cpp
  src/aruco/ippe.cpp
  src/aruco/marker.cpp
  src/aruco/markerdetector.cpp
  src/aruco/markerdetector_impl.cpp
  src/aruco/markerlabeler.cpp
  src/aruco/markermap.cpp
  src/aruco/posetracker.cpp
)

SET(headers
  include/aruco/aruco.h
  include/aruco/aruco_cvversioning.h
  include/aruco/aruco_export.h
  include/aruco/cameraparameters.h
  include/aruco/cvdrawingutils.h
  include/aruco/debug.h
  include/aruco/dictionary.h
  include/aruco/dictionary_based.h
  include/aruco/fractaldetector.h
  include/aruco/ippe.h
  include/aruco/levmarq.h
  include/aruco/marker.h
  include/aruco/markerdetector.h
  include/aruco/markerdetector_impl.h
  include/aruco/markerlabeler.h
  include/aruco/markermap.h
  include/aruco/picoflann.h
  include/aruco/posetracker.h
  include/aruco/timers.h
)

set(fractal_sources
  src/aruco/fractallabelers/fractallabeler.cpp
  src/aruco/fractallabelers/fractalmarker.cpp
  src/aruco/fractallabelers/fractalmarkerset.cpp
  src/aruco/fractallabelers/fractalposetracker.cpp
)

set(fractal_headers
  include/aruco/fractallabelers/fractalposetracker.h
  include/aruco/fractallabelers/fractalmarkerset.h
  include/aruco/fractallabelers/fractalmarker.h
  include/aruco/fractallabelers/fractallabeler.h
)

add_library(aruco
  ${sources}
  ${headers}
  ${fractal_sources}
  ${fractal_headers}
)

add_subdirectory(src/utils)
add_subdirectory(src/utils_calibration)
add_subdirectory(src/utils_fractal)
add_subdirectory(src/utils_markermap)

#############
## Install ##
#############
IF(NOT ARUCO_ANDROID)

set_target_properties(aruco PROPERTIES          # create *nix style library versions + symbolic links
    DEFINE_SYMBOL ARUCO_DSO_EXPORTS
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_SOVERSION}
    CLEAN_DIRECT_OUTPUT 1                       # allow creating static and shared libs without conflicts
    OUTPUT_NAME "aruco${PROJECT_DLLVERSION}"    # avoid conflicts between library and binary target names
)

#include(GenerateExportHeader)
#generate_export_header(aruco)

target_link_libraries(aruco
  ${catkin_LIBRARIES} ${OpenCV_LIBRARIES}
)

# target_link_libraries(aruco PUBLIC opencv_core)
# target_link_libraries(aruco PRIVATE opencv_imgproc opencv_calib3d opencv_features2d opencv_ml)

target_include_directories(aruco PUBLIC
#  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
#  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>
)

install(TARGETS aruco EXPORT arucoConfig
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION} PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
    ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(DIRECTORY include/
   DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
   FILES_MATCHING PATTERN "*.h"
)

install(EXPORT arucoConfig DESTINATION "share/aruco/cmake")
ELSE()
MESSAGE(STATUS  "ARUCO COMPILATION")
target_link_libraries( # Specifies the target library.
                       aruco lib_opencv

                       # Links the target library to the log library
                       # included in the NDK.
                       ${log-lib} )

ENDIF()

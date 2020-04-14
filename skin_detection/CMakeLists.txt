PROJECT(skin_detection)

cmake_minimum_required(VERSION 3.5)

SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)

SET(OpenCV_DIR /home/sky/environment/OpenCV/opencv/lib/cmake/opencv4)

find_package(OpenCV REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(skin_detection src/skin_detection.cpp)

link_directories(${OpenCV_LIBRARY_DIRS})

target_link_libraries(skin_detection ${OpenCV_LIBS})
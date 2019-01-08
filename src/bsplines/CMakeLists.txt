cmake_minimum_required(VERSION 2.4.6)
project(bsplines)

if(USE_ROSBUILD)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)
set(ROS_BUILD_TYPE Release)

rosbuild_init()

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#uncomment if you have defined messages
#rosbuild_genmsg()
#uncomment if you have defined services
#rosbuild_gensrv()

#common commands for building c++ executables and libraries
rosbuild_add_library(${PROJECT_NAME} 
    src/BSpline.cpp
    src/Function.cpp
    # src/SplinePath.cpp
    # src/SplineRegister.cpp
)
target_link_libraries(${PROJECT_NAME} gsl gslcblas)
#rosbuild_add_boost_directories()
#rosbuild_link_boost(${PROJECT_NAME} thread)

rosbuild_add_executable(test_approx test/function_approx.cpp)
target_link_libraries(test_approx ${PROJECT_NAME})

rosbuild_add_executable(test1 test/test.cpp)
target_link_libraries(test1 ${PROJECT_NAME})

rosbuild_add_executable(testsp test/testsp.cpp)
target_link_libraries(testsp ${PROJECT_NAME})

rosbuild_add_executable(optimsp test/optimsp.cpp)
target_link_libraries(optimsp ${PROJECT_NAME})

rosbuild_add_executable(function test/function.cpp)
target_link_libraries(function ${PROJECT_NAME})

rosbuild_add_executable(travmaps test/travmaps.cpp)
target_link_libraries(travmaps ${PROJECT_NAME})

else()
    find_package(catkin REQUIRED COMPONENTS rosconsole roscpp)
    catkin_package(
        INCLUDE_DIRS include
        LIBRARIES ${PROJECT_NAME}
        DEPENDS gsl gslcblas
    )
    find_path(GSL_INCLUDE_DIR gsl/gsl_version.h
        /usr/local/include
        /usr/include
        )
    find_library(GSL_LIBRARIES NAMES gsl PATHS /usr/lib /usr/local/lib)
    find_library(GSLCBLAS_LIBRARIES NAMES gslcblas PATHS /usr/lib /usr/local/lib)
    set(GSL_LIBRARIES ${GSL_LIBRARIES} ${GSLCBLAS_LIBRARIES})


    add_definitions(-Wall -g -ggdb -O3)
    include_directories(include ${GSL_INCLUDE_DIR})

    #common commands for building c++ executables and libraries
    add_library(${PROJECT_NAME} 
        src/BSpline.cpp
        src/Function.cpp
        src/SplinePath.cpp
        # src/SplineRegister.cpp
    )
    link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES} ${GSL_LIBRARIES})
    #rosbuild_add_boost_directories()
    #rosbuild_link_boost(${PROJECT_NAME} thread)

    add_executable(test1 test/test.cpp)
    target_link_libraries(test1 ${PROJECT_NAME} ${catkin_LIBRARIES} ${GSL_LIBRARIES})

    add_executable(testsp test/testsp.cpp)
    target_link_libraries(testsp ${PROJECT_NAME} ${GSL_LIBRARIES})

    # add_executable(optimsp test/optimsp.cpp)
    # target_link_libraries(optimsp ${PROJECT_NAME} ${GSL_LIBRARIES})

    add_executable(test_function test/function.cpp)
    target_link_libraries(test_function ${PROJECT_NAME} ${GSL_LIBRARIES})

    add_executable(test_approx test/function_approx.cpp)
    target_link_libraries(test_approx ${PROJECT_NAME} ${GSL_LIBRARIES})

    # add_executable(travmaps test/travmaps.cpp)
    # target_link_libraries(travmaps ${PROJECT_NAME} ${GSL_LIBRARIES})

endif()

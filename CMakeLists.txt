cmake_minimum_required(VERSION 2.8.3)
project(benchmark_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(
  benchmark_src
  URL https://github.com/google/benchmark/archive/v1.1.0.zip
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../benchmark_src &&
     cmake .
       -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
       -DBUILD_SHARED_LIBS:BOOL=true
       -DBENCHMARK_NAMESPACE:STRING=google
       -DCMAKE_BUILD_TYPE:STRING=Release
  BUILD_COMMAND cd ../benchmark_src && make -j 8
  INSTALL_COMMAND cd ../benchmark_src && $(MAKE) install -j 8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} benchmark_src)
target_link_libraries(${PROJECT_NAME}
${CATKIN_DEVEL_PREFIX}/lib/libbenchmark${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()
cs_export(
    INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
    CFG_EXTRAS benchmark-extras.cmake
)

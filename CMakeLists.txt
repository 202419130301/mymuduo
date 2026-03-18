cmake_minimum_required(VERSION 3.10)

# 项目名
project(mymuduo)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 设置库输出目录
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib)

# 收集所有 cpp 文件
file(GLOB SOURCES "*.cpp")

# 生成动态库
add_library(mymuduo SHARED ${SOURCES})
target_link_libraries(mymuduo PUBLIC pthread)

# 如果需要设置头文件搜索路径（.h在同目录，其实可选）
target_include_directories(mymuduo PUBLIC ${CMAKE_SOURCE_DIR})

option(BUILD_EXAMPLES "Build example programs" ON)
if(BUILD_EXAMPLES)
  add_executable(echo_server examples/echo_server.cpp)
  target_include_directories(echo_server PRIVATE ${CMAKE_SOURCE_DIR})
  target_link_libraries(echo_server PRIVATE mymuduo pthread)
endif()

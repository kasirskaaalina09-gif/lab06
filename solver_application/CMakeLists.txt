cmake_minimum_required(VERSION 3.10)

project(solver_application)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if(APPLE)
  add_executable(solver_app MACOSX_BUNDLE equation.cpp)
else()
  add_executable(solver_app equation.cpp)
endif()

target_link_libraries(solver_app formatter_ex solver)

install(TARGETS solver_app
    BUNDLE DESTINATION .
    RUNTIME DESTINATION bin)

if(APPLE)
  add_executable(solver MACOSX_BUNDLE equation.cpp)
else()
  add_executable(solver equation.cpp)
endif()

target_link_libraries(solver formatter_ex solver)

install(TARGETS solver
    BUNDLE DESTINATION .
    RUNTIME DESTINATION bin)

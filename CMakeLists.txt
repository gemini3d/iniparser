cmake_minimum_required(VERSION 3.13...3.25)

project(iniparser
LANGUAGES C
HOMEPAGE_URL https://github.com/ndevilla/iniparser
VERSION 4.1.0.3
)

enable_testing()

include(cmake/options.cmake)

add_library(iniparser src/iniparser.c src/dictionary.c)
target_include_directories(iniparser PUBLIC
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
$<INSTALL_INTERFACE:include>
)
target_compile_definitions(iniparser PRIVATE
$<$<BOOL:${MSVC}>:_CRT_SECURE_NO_WARNINGS>
)

# for FetchContent use of this library
add_library(iniparser::iniparser INTERFACE IMPORTED GLOBAL)
target_link_libraries(iniparser::iniparser INTERFACE iniparser)

install(TARGETS iniparser EXPORT ${PROJECT_NAME}-targets
LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
install(FILES src/iniparser.h src/dictionary.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

if(BUILD_TESTING)
  add_executable(test_iniparser example/iniexample.c)
  target_link_libraries(test_iniparser PRIVATE iniparser)
  target_compile_definitions(test_iniparser PRIVATE
  $<$<BOOL:${MSVC}>:_CRT_SECURE_NO_WARNINGS>
  )
  add_test(NAME IniParser COMMAND test_iniparser)

  add_executable(test_parse example/parse.c)
  target_link_libraries(test_parse PRIVATE iniparser)
  add_test(NAME Parse COMMAND test_parse)
  set_tests_properties(Parse PROPERTIES
  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/example
  TIMEOUT 5
  )
endif()

include(cmake/install.cmake)

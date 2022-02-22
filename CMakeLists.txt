cmake_minimum_required(VERSION 3.15...3.23)

project(iniparser
LANGUAGES C
HOMEPAGE_URL https://github.com/ndevilla/iniparser
VERSION 4.1.0.0
)

include(CTest)

include(cmake/options.cmake)

add_library(iniparser src/iniparser.c src/dictionary.c)
target_include_directories(iniparser PUBLIC
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
$<INSTALL_INTERFACE:include>
)

# for FetchContent use of this library
add_library(iniparser::iniparser INTERFACE IMPORTED GLOBAL)
target_link_libraries(iniparser::iniparser INTERFACE iniparser)

install(TARGETS iniparser EXPORT ${PROJECT_NAME}-targets)
install(FILES src/iniparser.h src/dictionary.h TYPE INCLUDE)

if(BUILD_TESTING)
  add_executable(test_iniparser example/iniexample.c)
  target_link_libraries(test_iniparser PRIVATE iniparser)
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

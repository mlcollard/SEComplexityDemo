# Build for srcComplexity

cmake_minimum_required(VERSION 3.14.0)

project(srcComplexity VERSION "1.0.0")

find_package(LibXml2 REQUIRED)

# Build srcComplexity
add_executable(srcComplexity srcComplexity.cpp)
target_link_libraries(srcComplexity PUBLIC srcmlxpath_shared)
install(TARGETS srcComplexity RUNTIME)

# Static srcmlxpath library
add_library(srcmlxpath STATIC srcMLXPathCount.cpp)
target_include_directories(srcmlxpath PUBLIC ${LIBXML2_INCLUDE_DIR})
target_link_libraries(srcmlxpath PUBLIC ${LIBXML2_LIBRARY})
install(TARGETS srcmlxpath LIBRARY)

# Shared srcmlxpath library
add_library(srcmlxpath_shared SHARED srcMLXPathCount.cpp)
target_include_directories(srcmlxpath_shared PUBLIC ${LIBXML2_INCLUDE_DIR})
target_link_libraries(srcmlxpath_shared PUBLIC ${LIBXML2_LIBRARY})
set_target_properties(srcmlxpath_shared PROPERTIES OUTPUT_NAME srcmlxpath)
set_target_properties(srcmlxpath_shared PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(srcmlxpath_shared PROPERTIES SOVERSION ${PROJECT_VERSION_MAJOR})
install(TARGETS srcmlxpath_shared LIBRARY)

# run example
set(SRCCOMPLEXITY_TESTFILE srcMLXPathCount.cpp.xml)
configure_file(${SRCCOMPLEXITY_TESTFILE} ${SRCCOMPLEXITY_TESTFILE} COPYONLY)
add_custom_target(run DEPENDS srcComplexity
                      COMMAND ./srcComplexity ${SRCCOMPLEXITY_TESTFILE})

# strip executables and shared libraries
# Verify that it works
set(CPACK_STRIP_FILES ON)

# Copyright file
set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_SOURCE_DIR}/COPYING.txt)
install(FILES ${CMAKE_SOURCE_DIR}/COPYING.txt DESTINATION share/doc/srccomplexity RENAME copyright)

# Description
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Calculate source code complexity")
file(WRITE ${CMAKE_BINARY_DIR}/description.txt
"srcComplexity Hi
")
set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_BINARY_DIR}/description.txt)

include(GNUInstallDirs)

set(CPACK_PACKAGE_NAME "srccomplexity")
set(CPACK_PACKAGE_VERSION_MAJOR "1")
set(CPACK_PACKAGE_VERSION_MINOR "0")
set(CPACK_PACKAGE_VERSION_PATCH "0")
set(CPACK_PACKAGE_CONTACT "Michael L. Collard <collard@uakron.edu>")
include(CPack)

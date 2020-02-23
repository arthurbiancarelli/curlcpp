
cmake_minimum_required(VERSION 3.0)
#if using an older VERSION of curl ocsp stapling can be disabled
set(CURL_MIN_VERSION "7.28.0")
# Setting up project
project(curlcpp LANGUAGES CXX VERSION 0.0.1)


# Add MacPorts
if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set(CMAKE_PREFIX_PATH /opt/local ${CMAKE_PREFIX_PATH})
endif()

# Checking for dependencies
find_package(CURL ${CURL_MIN_VERSION} REQUIRED)


# Set up source directories
add_subdirectory(include)
add_subdirectory(src)

# Set up include paths
include_directories(
        ${PROJECT_SOURCE_DIR}/include
        ${PROJECT_SOURCE_DIR}/src
)

add_library(${PROJECT_NAME} ${CURLCPP_SOURCE} ${PUBLIC_HEADERS})
target_link_libraries(${PROJECT_NAME} CURL::libcurl)

set_target_properties(${PROJECT_NAME} PROPERTIES CXX_STANDARD 11
				  CXX_STANDARD_REQUIRED ON
				  CXX_STANDARD_REQUIRED ON
				  CXX_EXTENSIONS OFF
				  CXX_EXTENSIONS OFF

				  PUBLIC_HEADER "${PUBLIC_HEADERS}"
				  RUNTIME_OUTPUT_DIRECTORY "bin"
				  LIBRARY_OUTPUT_DIRECTORY "bin"
				  ARCHIVE_OUTPUT_DIRECTORY "lib"
				  DEBUG_POSTFIX "d"
				  MINSIZEREL_POSTFIX "msr"
				  RELWITHDEBINFO_POSTFIX "rwdi")
				  
# If CMAKE_INSTALL_PREFIX is not specified, GNU install directories will be used
include(GNUInstallDirs)

# Specify where library and headers will be installed
install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Targets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
)

target_include_directories(${PROJECT_NAME} PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# Create ${PROJECT_NAME}ConfigVersion package used to specify package version
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/cmake/${PROJECT_NAME}ConfigVersion.cmake"
  VERSION ${VERSION}
  COMPATIBILITY AnyNewerVersion
)

# Install ${PROJECT_NAME}ConfigVersion file
install(
  FILES
  ${CMAKE_CURRENT_BINARY_DIR}/cmake/${PROJECT_NAME}ConfigVersion.cmake
  DESTINATION ${CMAKE_INSTALL_PREFIX}/share/${PROJECT_NAME}
  COMPONENT devel
)

# Specifying config file that will be used to find a library using find_package().
export(TARGETS ${PROJECT_NAME}
	FILE ${PROJECT_NAME}Config.cmake
)

# Install config file
install (EXPORT ${PROJECT_NAME}Targets
	FILE ${PROJECT_NAME}Config.cmake
	DESTINATION ${CMAKE_INSTALL_PREFIX}/share/${PROJECT_NAME}
)
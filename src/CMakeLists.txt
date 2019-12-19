set(CURLCPP_SOURCE
  curl_easy.cpp
  curl_header.cpp
  curl_global.cpp
  curl_form.cpp
  curl_multi.cpp
  curl_share.cpp
  curl_info.cpp
  curl_cookie.cpp
  curl_exception.cpp
  cookie.cpp
  cookie_date.cpp
  cookie_time.cpp
  cookie_datetime.cpp
)


if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
endif()

add_library(${PROJECT_NAME} ${CURLCPP_SOURCE} ${PUBLIC_HEADERS})
target_link_libraries(${PROJECT_NAME} libcurl::libcurl)

set_target_properties(${PROJECT_NAME} PROPERTIES CXX_STANDARD 14
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
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
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
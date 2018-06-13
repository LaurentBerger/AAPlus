cmake_minimum_required(VERSION 3.0)

set(AALib_VERSION_MAJOR 1 CACHE STRING "major version" FORCE)
set(AALib_VERSION_MINOR 95 CACHE STRING "minor version" FORCE)
set(AALib_VERSION ${AALib_VERSION_MAJOR}.${AALib_VERSION_MINOR} CACHE STRING "version" FORCE)


set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_INCLUDE_CURRENT_DIR_IN_INTERFACE ON)

include(GenerateExportHeader)


file(GLOB AALib_SRCS     "source/*.cpp")
file(GLOB AALib_Headers     "include/*.h" "include/*.hpp")

message ("AALib_VERSION ${AALib_VERSION}")
message ("CMAKE_CURRENT_BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR}")
message ("CMAKE_CURRENT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}")
message ("CMAKE_CONFIGURATION_TYPES ${CMAKE_CONFIGURATION_TYPES}")


add_library (AALib STATIC ${AALib_SRCS})


if (DEFINED CMAKE_CONFIGURATION_TYPES)
  # multi-config handling
  set (CMAKE_BUILD_CONFIG \${BUILD_TYPE})
  set (CMAKE_CFG_BUILD_CONFIG ${CMAKE_CFG_INTDIR})
else()
  # mono config handling
  if (CMAKE_BUILD_TYPE)
set (CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE} CACHE STRING "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel" FORCE)
  else()
set (CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build, options are: None(CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel" FORCE)
  endif()
  set (CMAKE_BUILD_CONFIG ${CMAKE_BUILD_TYPE})
  set (CMAKE_CFG_BUILD_CONFIG ${CMAKE_BUILD_TYPE})
endif()


add_custom_command (OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_BUILD_CONFIG}/output_file"
                    COMMAND "${CMAKE_COMMAND}" -DBUILD_TYPE=$<CONFIG> -P script.cmake
                    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                    COMMENT "Generating output_file")


target_include_directories(AALib PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:include>
	PRIVATE source)

generate_export_header( AALib )

set_property(TARGET AALib PROPERTY VERSION ${AALib_VERSION})

install(TARGETS AALib EXPORT AALibConfig
  LIBRARY DESTINATION "lib/\${CMAKE_INSTALL_CONFIG_NAME}"
  ARCHIVE DESTINATION "lib/\${CMAKE_INSTALL_CONFIG_NAME}"
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)


install(FILES "${CMAKE_CURRENT_BINARY_DIR}/AALib_export.h"  DESTINATION include   COMPONENT    Devel)
install(FILES ${AALib_Headers} DESTINATION include)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/AALib/AALibConfigVersion.cmake"
  VERSION ${AALib_VERSION}
  COMPATIBILITY AnyNewerVersion
)

#export(TARGETS AALib   FILE "${CMAKE_CURRENT_BINARY_DIR}/AALib/AALibConfig.cmake" )

#install(EXPORT AALibConfig DESTINATION AALib/cmake)

set(FOO "rtatzera")
#configure_file(AALibTargets.cmake   "${CMAKE_CURRENT_BINARY_DIR}/AALib/AALibConfig.cmake"  COPYONLY)

set(ConfigPackageLocation lib/cmake/AALib)

message ("ConfigPackageLocation ${ConfigPackageLocation}")

install(EXPORT AALibConfig
  FILE
    AALibConfig.cmake
  DESTINATION
    ${ConfigPackageLocation}
)
#https://stackoverflow.com/questions/29971026/generator-expression-for-install-commands
message ( " dddd $ENV{DESTDIR}${CMAKE_INSTALL_PREFIX}")

install(SCRIPT cmake/patch_export_files.cmake DESTINATION
    ${ConfigPackageLocation})
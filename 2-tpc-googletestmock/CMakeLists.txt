cmake_minimum_required( VERSION 3.16.3 )

project( jsd-tpc-googletestmock C CXX )


JSD_SHOW_PROJECT_HEADER()


include( ${JSD_SSC_CMAKE_PATH}/tpc-googletestmock-set-compiler-settings.cmake )

set( JSD_INCLUDED_PROJECT_HEADER_PATH ${JSD_SSC_SOURCE_PATH}/googletest/include )
set( JSD_INCLUDED_PROJECT_SOURCE_PATH ${JSD_SSC_SOURCE_PATH}/googletest/src )
set( JSD_THIS_PROJECT_HEADER_PATH ${JSD_SSC_SOURCE_PATH}/googlemock/include )
set( JSD_THIS_PROJECT_SOURCE_PATH ${JSD_SSC_SOURCE_PATH}/googlemock/src )

list( APPEND ${PROJECT_NAME}_TEST_SOURCE_DIRS_VAR ${JSD_COMPONENT_SOURCE_ROOT_PATH}/googletest/test )
list( APPEND ${PROJECT_NAME}_TEST_SOURCE_DIRS_VAR ${JSD_COMPONENT_SOURCE_ROOT_PATH}/googlemock/test )

###set( JSD_THIS_SOURCE_PATH ${JSD_SSC_SOURCE_PATH} )

## include dependencies
set( ${PROJECT_NAME}_DEPENDENCY_DIRS_VAR
	${JSD_PLATFORM_SOURCES}
	${JSD_INCLUDED_PROJECT_HEADER_PATH}
	${JSD_THIS_PROJECT_HEADER_PATH} )
list( REMOVE_DUPLICATES ${PROJECT_NAME}_DEPENDENCY_DIRS_VAR )

include_directories(
	${${PROJECT_NAME}_DEPENDENCY_DIRS_VAR}
	${JSD_SSC_SOURCE_PATH}/googletest )


## this project headers and sources enumeration section
file( GLOB_RECURSE header_and_source_files
	${JSD_INCLUDED_PROJECT_HEADER_PATH}/*.h* ${JSD_INCLUDED_PROJECT_HEADER_PATH}/*.c*
	${JSD_INCLUDED_PROJECT_SOURCE_PATH}/*.h* ${JSD_INCLUDED_PROJECT_SOURCE_PATH}/*.c*
	${JSD_THIS_PROJECT_HEADER_PATH}/*.h* ${JSD_THIS_PROJECT_HEADER_PATH}/*.c*
	${JSD_THIS_PROJECT_SOURCE_PATH}/*.h* ${JSD_THIS_PROJECT_SOURCE_PATH}/*.c* )

set( JSD_THIS_PROJECT_FILES_TO_REMOVE
	"gtest-type-util.h.pump"
	"gtest-all.cc"
	"gtest_main.cc"
	"gmock-generated-actions.h.pump"
	"gmock-generated-function-mockers.h.pump"
	"gmock-generated-matchers.h.pump"
	"gmock-all.cc"
	"gmock_main.cc" )
JSD_REMOVE_FILES_FROM_THE_LIST( header_and_source_files JSD_THIS_PROJECT_FILES_TO_REMOVE )

add_library( ${PROJECT_NAME} SHARED ${header_and_source_files} )


## definition section
set( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR
	-DGTEST_CREATE_SHARED_LIBRARY )
JSD_LIST_TO_STRING( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR )
JSD_STRING_REMOVE_DUPLICATES( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR )
set( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR
	-DGTEST_LINKED_AS_SHARED_LIBRARY )
JSD_LIST_TO_STRING( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR )
JSD_STRING_REMOVE_DUPLICATES( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR )
add_definitions( "${${PROJECT_NAME}_BUILD_DEFINITIONS_VAR}" )


## required to be able to be linked into shared libraries.
set_target_properties( ${PROJECT_NAME} PROPERTIES POSITION_INDEPENDENT_CODE ON )
set_target_properties( ${PROJECT_NAME} PROPERTIES VERSION 1.10.0 )
set_target_properties( ${PROJECT_NAME} PROPERTIES SOVERSION 1.10 )


## project target section
if ( UNIX )
	set( ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR
		"pthread" )
else()
	set( ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR
		"" )
endif()
list( REMOVE_DUPLICATES ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR )
target_link_libraries( ${PROJECT_NAME} ${${PROJECT_NAME}_DEPENDENCY_LIBS_VAR} )


## Expose public includes to other subprojects through cache variable.
include( ${JSD_CMAKE_PATH}/set-expose-dependencies.cmake )
include( ${JSD_CMAKE_PATH}/set-expose-test-source-directories.cmake )
JSD_SHOW_PROJECT_FOOTER()

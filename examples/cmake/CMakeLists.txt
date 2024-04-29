cmake_minimum_required(VERSION 3.16)

project(fcgi-test)
include("${CMAKE_SOURCE_DIR}/${CPL_BUILDIT_DIR_REL}/buildit.cmake" OPTIONAL)
find_package(CplCMakeModules REQUIRED)

cpl_legacy_warning()

if (NOT CPL_DISABLE_TESTS_ALL)
	cpl_add_test()
	cpl_link_package(FcgiSmf)
	cpl_sources(main.c)
endif()

########################################################################################################################
# Complete
cpl_project_complete()

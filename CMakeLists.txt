cmake_minimum_required(VERSION 3.16)

########################################################################################################################
# project
project(fcgi VERSION 2.4.2)
include("${CMAKE_SOURCE_DIR}/${CPL_BUILDIT_DIR_REL}/buildit.cmake" OPTIONAL)
include(CheckIncludeFiles)
find_package(CplCMakeModules REQUIRED)

if(UNIX)
	cpl_packages_get_component_string(${PROJECT_NAME} dev component_dev)
	cpl_packages_add_component(${component_dev})

	configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libfcgi/${PROJECT_NAME}-config.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake @ONLY)

    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake DESTINATION "${CMAKE_INSTALL_EXPORTDIR}/${PROJECT_NAME}" COMPONENT ${component_dev})

	set(${PROJECT_NAME}_DIR "${CMAKE_CURRENT_BINARY_DIR}" CACHE PATH "${name} config directory" FORCE)
    export(PACKAGE ${PROJECT_NAME})

else()

	if (UNIX)
		set(CMAKE_C_VISIBILITY_PRESET default CACHE STRING "" FORCE)
		set(CMAKE_CXX_VISIBILITY_PRESET default CACHE STRING "" FORCE)
	endif()

	########################################################################################################################
	# Libraries, Interfaces, Executables and Tests
	cpl_legacy_warning()
	cpl_add_library()

	########################################################################################################################
	# Packages and Link
	cpl_link_package(LIBS Ws2_32 NOFIND IF WIN32)

	########################################################################################################################
	# Settings commands
	cpl_include_directories(PRIVATE libfcgi)

	configure_file(include/fcgi_config_x86.h ${CMAKE_CURRENT_BINARY_DIR}/fcgi_config.h COPYONLY)
	cpl_add_api_file(
		${CMAKE_CURRENT_BINARY_DIR}/fcgi_config.h
		include/fastcgi.h
		include/fcgi_stdio.h
		include/fcgiapp.h
		include/fcgimisc.h
		include/fcgio.h
		include/fcgios.h
	)

	cpl_sources(
		libfcgi/fcgi_stdio.c
		libfcgi/fcgiapp.c
		libfcgi/fcgio.cpp
		libfcgi/strerror.c
	)

	cpl_sources(
		libfcgi/os_unix.c
		IF UNIX
	)
	cpl_sources(
		libfcgi/os_win32.c
		IF WIN32
	)

	cpl_compile_definitions(SUFFIX shared PRIVATE "DLLAPI=__declspec(dllexport)" IF WIN32)
	cpl_compile_definitions(SUFFIX static PUBLIC "FCGI_STATIC")


	if (UNIX)
		CHECK_INCLUDE_FILES(netinet/in.h HAVE_NETINET_IN_H)
		cpl_compile_definitions(PRIVATE HAVE_NETINET_IN_H=1 IF HAVE_NETINET_IN_H)

		CHECK_INCLUDE_FILES(netdb.h HAVE_NETDB_H)
		cpl_compile_definitions(PRIVATE HAVE_NETDB_H=1 IF HAVE_NETDB_H)

		CHECK_INCLUDE_FILES(arpa/inet.h HAVE_ARPA_INET_H)
		cpl_compile_definitions(PRIVATE HAVE_ARPA_INET_H=1 IF HAVE_ARPA_INET_H)

		CHECK_INCLUDE_FILES(dlfcn.h HAVE_DLFCN_H)
		cpl_compile_definitions(PRIVATE HAVE_DLFCN_H=1 IF HAVE_DLFCN_H)

		CHECK_INCLUDE_FILES(inttypes.h HAVE_INTTYPES_H)
		cpl_compile_definitions(PRIVATE HAVE_INTTYPES_H=1 IF HAVE_INTTYPES_H)

		CHECK_INCLUDE_FILES(memory.h HAVE_MEMORY_H)
		cpl_compile_definitions(PRIVATE HAVE_MEMORY_H=1 IF HAVE_MEMORY_H)

		CHECK_INCLUDE_FILES(sys/un.h HAVE_SOCKADDR_UN_SUN_LEN)
		cpl_compile_definitions(PRIVATE HAVE_SOCKADDR_UN_SUN_LEN=1 IF HAVE_SOCKADDR_UN_SUN_LEN)

		CHECK_INCLUDE_FILES(stdint.h HAVE_STDINT_H)
		cpl_compile_definitions(PRIVATE HAVE_STDINT_H=1 IF HAVE_STDINT_H)

		CHECK_INCLUDE_FILES(stdlib.h HAVE_STDLIB_H)
		cpl_compile_definitions(PRIVATE HAVE_STDLIB_H=1 IF HAVE_STDLIB_H)

		CHECK_INCLUDE_FILES(strings.h HAVE_STRINGS_H)
		cpl_compile_definitions(PRIVATE HAVE_STRINGS_H=1 IF HAVE_STRINGS_H)

		CHECK_INCLUDE_FILES(string.h HAVE_STRING_H)
		cpl_compile_definitions(PRIVATE HAVE_STRING_H=1 IF HAVE_STRING_H)

		CHECK_INCLUDE_FILES(sys/param.h HAVE_SYS_PARAM_H)
		cpl_compile_definitions(PRIVATE HAVE_SYS_PARAM_H=1 IF HAVE_SYS_PARAM_H)

		CHECK_INCLUDE_FILES(sys/socket.h HAVE_SYS_SOCKET_H)
		cpl_compile_definitions(PRIVATE HAVE_SYS_SOCKET_H=1 IF HAVE_SYS_SOCKET_H)

		CHECK_INCLUDE_FILES(sys/stat.h HAVE_SYS_STAT_H)
		cpl_compile_definitions(PRIVATE HAVE_SYS_STAT_H=1 IF HAVE_SYS_STAT_H)

		CHECK_INCLUDE_FILES(sys/time.h HAVE_SYS_TIME_H)
		cpl_compile_definitions(PRIVATE HAVE_SYS_TIME_H=1 IF HAVE_SYS_TIME_H)

		CHECK_INCLUDE_FILES(sys/types.h HAVE_SYS_TYPES_H)
		cpl_compile_definitions(PRIVATE HAVE_SYS_TYPES_H=1 IF HAVE_SYS_TYPES_H)

		CHECK_INCLUDE_FILES(unistd.h HAVE_UNISTD_H)
		cpl_compile_definitions(PRIVATE HAVE_UNISTD_H=1 IF HAVE_UNISTD_H)

	endif()
endif()


add_subdirectory(examples/cmake)

########################################################################################################################
# Complete
cpl_packages_setup()
cpl_project_complete()

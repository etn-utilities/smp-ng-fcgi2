cmake_minimum_required(VERSION 3.16)

########################################################################################################################
# project
project(fcgi VERSION 2.4.2)
include("${CMAKE_SOURCE_DIR}/${CPL_BUILDIT_DIR_REL}/buildit.cmake" OPTIONAL)
find_package(CplCMakeModules REQUIRED)

if(UNIX)
	cpl_packages_get_component_string(${PROJECT_NAME} dev component_dev)
	cpl_packages_add_component(${component_dev})

	configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libfcgi/${PROJECT_NAME}-config.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake @ONLY)

    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake DESTINATION "${CMAKE_INSTALL_EXPORTDIR}/${PROJECT_NAME}" COMPONENT ${component_dev})

	set(${PROJECT_NAME}_DIR "${CMAKE_CURRENT_BINARY_DIR}" CACHE PATH "${name} config directory" FORCE)
    export(PACKAGE ${PROJECT_NAME})

else()

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

    cpl_compile_definitions(SUFFIX shared PRIVATE "DLLAPI=__declspec(dllexport)")
    cpl_compile_definitions(SUFFIX static PUBLIC "FCGI_STATIC")

endif()

add_subdirectory(examples/cmake)

########################################################################################################################
# Complete
cpl_packages_setup()
cpl_project_complete()

cmake_minimum_required(VERSION 3.2)

project(xxtelebot
	LANGUAGES CXX
	VERSION 1.2.0.0)

# M.m
set(TGBOTAPI_VER_MAJOR 3)
set(TGBOTAPI_VER_MINOR 3)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/lib")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/lib")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/lib")

set(HEADERS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include")

if(NOT CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
	message(FATAL_ERROR "!! Compiler identification: ${CMAKE_CXX_COMPILER_ID} is not supported")
endif()

find_package(Doxygen)
if(DOXYGEN_FOUND)
	add_custom_target(docs doxygen Doxyfile
		DEPENDS "include/tgbot/version.h"
		WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})

	message(STATUS "Doxygen: use \"make docs\" to generate documentation")
endif ()

configure_file("version.h.in"
	"${HEADERS_DIR}/tgbot/version.h")

add_subdirectory(src)

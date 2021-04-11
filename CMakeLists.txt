# Copyright (C) 2007-2014 LuaDist.
# Created by Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( tolua C CXX)
cmake_minimum_required ( VERSION 3.0 )
enable_testing()

find_package ( Lua REQUIRED )
include_directories ( include src/lib ${LUA_INCLUDE_DIR} )

file ( GLOB SRC_LIBTOLUA src/lib/*.c )

add_library ( tolua_lib SHARED ${SRC_LIBTOLUA} )
target_link_libraries ( tolua_lib ${LUA_LIBRARIES} )
set_target_properties ( tolua_lib PROPERTIES OUTPUT_NAME tolua
	CLEAN_DIRECT_OUTPUT 1 )
if ( WIN32 )
  target_compile_definitions (tolua_lib PRIVATE TOLUA5_DLL)
endif ()

# Generate toluabind.c from lua sources
add_executable(tolua_lua src/bin/tolua.c)
target_compile_definitions (tolua_lua PRIVATE LUA_SOURCE)
target_link_libraries ( tolua_lua tolua_lib ${LUA_LIBRARIES} )
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/src/bin/toluabind.c
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/bin/tolua.pkg
  COMMAND tolua_lua
  ARGS -o toluabind.c tolua.pkg
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/bin
)

# Build app
include_directories ( src/bin )
set ( SRC_TOLUA src/bin/tolua.c src/bin/toluabind.c )
add_executable ( tolua ${SRC_TOLUA} )
target_link_libraries ( tolua tolua_lib ${LUA_LIBRARIES} )

add_subdirectory(src/tests tests)

set (TOLUA_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include CACHE INTERNAL "tolua headers")
set (TOLUA_LIBRARIES tolua_lib CACHE INTERNAL "tolua libraries")
set (TOLUA_EXECUTABLE tolua CACHE INTERNAL "tolua executable")

install(TARGETS tolua RUNTIME DESTINATION bin)
install(TARGETS tolua_lib LIBRARY DESTINATION lib)
install(DIRECTORY include DESTINATION include FILES_MATCHING PATTERN "*.h")


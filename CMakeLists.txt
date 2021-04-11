# Copyright (C) 2007-2014 LuaDist.
# Created by Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( tolua C CXX)
cmake_minimum_required ( VERSION 3.0 )

find_package ( Lua REQUIRED )
include_directories ( include src/lib ${LUA_INCLUDE_DIR} )

if (MSVC)
  add_compile_options(/D_CRT_SECURE_NO_WARNINGS /DTOLA_BUILD_AS_DLL)
endif(MSVC)

file ( GLOB SRC_LIBTOLUA src/lib/*.c )

add_library ( tolua_lib SHARED ${SRC_LIBTOLUA} )
target_link_libraries ( tolua_lib ${LUA_LIBRARIES} )
set_target_properties ( tolua_lib PROPERTIES OUTPUT_NAME tolua
	CLEAN_DIRECT_OUTPUT 1 )
if (WIN32)
  target_compile_definitions (tolua_lib PRIVATE TOLUA_LIB)
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

set (TOLUA_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include CACHE INTERNAL "tolua headers")
set (TOLUA_LIBRARIES tolua_lib CACHE INTERNAL "tolua libraries")
set (TOLUA_EXECUTABLE tolua CACHE INTERNAL "tolua executable")

install(TARGETS tolua RUNTIME DESTINATION bin)
install(TARGETS tolua_lib LIBRARY DESTINATION lib)
install(DIRECTORY include DESTINATION include FILES_MATCHING PATTERN "*.h")

enable_testing()
set(TESTS_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/tests)
function(tolua_generate module pkgfile output)
add_custom_command(
  COMMAND tolua
  ARGS -n ${module} -o ${CMAKE_CURRENT_BINARY_DIR}/${output} ${pkgfile}
  DEPENDS ${pkgfile}
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${output}
  WORKING_DIRECTORY ${TESTS_DIRECTORY}
)
endfunction()

tolua_generate(tdirective
  ${TESTS_DIRECTORY}/tdirective.pkg
  tdirectivebind.c
)
add_executable(tdirective tdirectivebind.c)
target_include_directories(tdirective PRIVATE ${TESTS_DIRECTORY})
target_link_libraries(tdirective tolua_lib ${LUA_LIBRARIES})
add_test(NAME tdirective COMMAND tdirective)

set (CTESTS tarray tenum tmodule tvariable)
foreach (TEST IN ITEMS ${CTESTS})
  tolua_generate(${TEST}
    ${TESTS_DIRECTORY}/${TEST}.pkg
    ${TEST}bind.c
  )
	add_executable(${TEST} ${TESTS_DIRECTORY}/${TEST}.c ${TEST}bind.c)
  target_include_directories(${TEST} PRIVATE ${TESTS_DIRECTORY})
	target_link_libraries(${TEST} tolua_lib ${LUA_LIBRARIES})
	add_test(NAME ${TEST} COMMAND ${TEST})
endforeach()

set (CXXTESTS tnamespace tclass tpeer tinheritance tconstant tfunction tvararg)
foreach (TEST IN ITEMS ${CXXTESTS})
  tolua_generate(${TEST}
    ${TESTS_DIRECTORY}/${TEST}.pkg
    ${TEST}bind.cpp
  )
	add_executable(${TEST} ${TESTS_DIRECTORY}/${TEST}.cpp ${TEST}bind.cpp)
  target_include_directories(${TEST} PRIVATE ${TESTS_DIRECTORY})
	target_link_libraries(${TEST} tolua_lib ${LUA_LIBRARIES})
	add_test(NAME ${TEST} COMMAND ${TEST})
endforeach()

set (TOLUA_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include CACHE INTERNAL "tolua headers")
set (TOLUA_LIBRARIES tolua_lib CACHE INTERNAL "tolua libraries")
set (TOLUA_EXECUTABLE tolua CACHE INTERNAL "tolua executable")


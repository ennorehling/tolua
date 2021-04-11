# Copyright (C) 2007-2014 LuaDist.
# Created by Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( tolua C CXX)
cmake_minimum_required ( VERSION 3.0 )

function(tolua_generate module pkgfile output)
add_custom_command(
  COMMAND tolua
  ARGS -n ${module} -o ${CMAKE_CURRENT_BINARY_DIR}/${output} ${pkgfile}
  DEPENDS ${pkgfile}
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${output}
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
)
endfunction()

tolua_generate(tdirective
  ${CMAKE_CURRENT_SOURCE_DIR}/tdirective.pkg
  tdirectivebind.c
)
add_executable(tdirective tdirectivebind.c)
target_include_directories(tdirective PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(tdirective tolua_lib ${LUA_LIBRARIES})
add_test(NAME tdirective COMMAND tdirective)

set (CTESTS tarray tenum tmodule tvariable)
foreach (TEST IN ITEMS ${CTESTS})
  tolua_generate(${TEST}
    ${CMAKE_CURRENT_SOURCE_DIR}/${TEST}.pkg
    ${TEST}bind.c
  )
	add_executable(${TEST} ${CMAKE_CURRENT_SOURCE_DIR}/${TEST}.c ${TEST}bind.c)
  target_include_directories(${TEST} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
	target_link_libraries(${TEST} tolua_lib ${LUA_LIBRARIES})
	add_test(NAME ${TEST} COMMAND ${TEST})
endforeach()

set (CXXTESTS tnamespace tclass tpeer tinheritance tconstant tfunction tvararg)
foreach (TEST IN ITEMS ${CXXTESTS})
  tolua_generate(${TEST}
    ${CMAKE_CURRENT_SOURCE_DIR}/${TEST}.pkg
    ${TEST}bind.cpp
  )
	add_executable(${TEST} ${CMAKE_CURRENT_SOURCE_DIR}/${TEST}.cpp ${TEST}bind.cpp)
  target_include_directories(${TEST} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
	target_link_libraries(${TEST} tolua_lib ${LUA_LIBRARIES})
	add_test(NAME ${TEST} COMMAND ${TEST})
endforeach()

set (TOLUA_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include CACHE INTERNAL "tolua headers")
set (TOLUA_LIBRARIES tolua_lib CACHE INTERNAL "tolua libraries")
set (TOLUA_EXECUTABLE tolua CACHE INTERNAL "tolua executable")


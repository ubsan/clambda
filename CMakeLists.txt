cmake_minimum_required(VERSION 3.6)

project(clambda VERSION 0.1 LANGUAGES C)

add_library(ubclib
  source/ubclib/format.c)

add_library(clambda
  source/clambda/parse_ast.c)

if (NOT MSVC)
  target_compile_features(ubclib PUBLIC c_std_99)
  target_compile_features(clambda PUBLIC c_std_99)
endif()

target_link_libraries(clambda ubclib)

target_include_directories(ubclib
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  PRIVATE
    source/ubclib)

target_include_directories(clambda
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  PRIVATE
    source/clambda)

add_executable(clambdac
  source/main.c)

target_link_libraries(clambdac ubclib clambda)

if(MSVC)
  # hack to deal with cmake automatically inserting /W3; taken from llvm
  string(REGEX REPLACE " /W[0-4]" "" CMAKE_C_FLAGS "${CMAKE_C_FLAGS}")
endif()

function(add_options to)
  if(MSVC)
    target_compile_options(${to}
      PRIVATE
        /W4
        /WX
      PUBLIC
        /permissive-
        /wd4201
        /wd4204)
  else()
    target_compile_options(${to}
      PRIVATE
        -Wall
        -Wextra
        -pedantic
        -Werror)
  endif()
endfunction()

add_options(ubclib)
add_options(clambda)
add_options(clambdac)

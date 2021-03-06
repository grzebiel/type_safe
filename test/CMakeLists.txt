# Copyright (C) 2016-2017 Jonathan Müller <jonathanmueller.dev@gmail.com>
# This file is subject to the license terms in the LICENSE file
# found in the top-level directory of this distribution.

if(NOT EXISTS ${CMAKE_CURRENT_BINARY_DIR}/catch.hpp)
    file(DOWNLOAD
         https://raw.githubusercontent.com/philsquared/Catch/master/single_include/catch.hpp
         ${CMAKE_CURRENT_BINARY_DIR}/catch.hpp
         SHOW_PROGESS
         STATUS status
         LOG log)

    list(GET status 0 status_code)
    list(GET status 1 status_string)

    if(NOT status_code EQUAL 0)
        message(FATAL_ERROR "error downloading catch: ${status_string}"
                "${log}")
    endif()
endif()

set(source_files test.cpp
                 arithmetic_policy.cpp
                 boolean.cpp
                 bounded_type.cpp
                 compact_optional.cpp
                 constrained_type.cpp
                 deferred_construction.cpp
                 downcast.cpp
                 flag.cpp
                 flag_set.cpp
                 floating_point.cpp
                 index.cpp
                 integer.cpp
                 narrow_cast.cpp
                 optional.cpp
                 optional_ref.cpp
                 output_parameter.cpp
                 reference.cpp
                 strong_typedef.cpp
                 tagged_union.cpp
                 types.cpp
                 variant.cpp
                 visitor.cpp)
add_executable(type_safe_test debugger_type.hpp ${source_files})
target_link_libraries(type_safe_test PUBLIC type_safe)
target_include_directories(type_safe_test PUBLIC ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR})

if(MSVC)
    target_compile_definitions(type_safe_test PRIVATE TYPE_SAFE_TEST_NO_STATIC_ASSERT)
elseif(CMAKE_COMPILER_IS_GNUCC AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 5.0)
    target_compile_definitions(type_safe_test PRIVATE TYPE_SAFE_TEST_NO_STATIC_ASSERT)
endif()

add_test(NAME test COMMAND type_safe_test)

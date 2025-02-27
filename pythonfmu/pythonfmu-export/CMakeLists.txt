cmake_minimum_required(VERSION 3.15)
project(pythonfmu-export VERSION 0.2.0)

# ==============================================================================
# Build settings
# ==============================================================================

set(BUILD_SHARED_LIBS ON)

# ==============================================================================
# Global internal configuration
# ==============================================================================

if (MSVC)
  # https://stackoverflow.com/questions/14172856/compile-with-mt-instead-of-md-using-cmake
  set(CompilerFlags
          CMAKE_CXX_FLAGS
          CMAKE_CXX_FLAGS_DEBUG
          CMAKE_CXX_FLAGS_RELEASE
          CMAKE_C_FLAGS
          CMAKE_C_FLAGS_DEBUG
          CMAKE_C_FLAGS_RELEASE
          )
  foreach (CompilerFlag ${CompilerFlags})
    string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
  endforeach ()
endif ()

# Automatically export all symbols in Windows DLLs.
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# ==============================================================================
# Dependencies
# ==============================================================================

# Force to use stable Python ABI https://docs.python.org/3/c-api/stable.html
add_compile_definitions(Py_LIMITED_API)
find_package(Python3 REQUIRED COMPONENTS Interpreter Development)

set(Python3_FIND_STRATEGY LOCATION)  # https://github.com/NTNU-IHB/PythonFMU/pull/135 https://github.com/NTNU-IHB/PythonFMU/pull/136 https://github.com/NTNU-IHB/PythonFMU/issues/128

if (WIN32)
  set(Python3_LIBRARIES ${Python3_LIBRARY_DIRS}/python3.lib)
else ()
  set(Python3_LIBRARIES ${Python3_LIBRARIES})
endif ()

if (WIN32)
  set(TARGET_PLATFORM win)
elseif (APPLE)
  set(TARGET_PLATFORM darwin)
else ()
  set(TARGET_PLATFORM linux)
endif ()

if ("${CMAKE_SIZEOF_VOID_P}" STREQUAL "8")
  set(TARGET_PLATFORM ${TARGET_PLATFORM}64)
else ()
  set(TARGET_PLATFORM ${TARGET_PLATFORM}32)
endif ()

message(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")
message(STATUS "TARGET_PLATFORM: ${TARGET_PLATFORM}")
message(STATUS "Python3_STDLIB: ${Python3_STDLIB}")
message(STATUS "Python3_LIBRARIES: ${Python3_LIBRARIES}")
message(STATUS "Python3_LIBRARY_DIRS: ${Python3_LIBRARY_DIRS}")
message(STATUS "Python3_INTERPRETER_ID: ${Python3_INTERPRETER_ID}")

message("Building pythonfmu-export for platform ${TARGET_PLATFORM}")

add_subdirectory(src)

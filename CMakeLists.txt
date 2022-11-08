cmake_minimum_required(VERSION 3.15)

include(cmake/prelude.cmake)

project(
    learnopengl
    VERSION 0.1.0
    DESCRIPTION "A project to learn OpenGL with"
    HOMEPAGE_URL "erarnitox.de"
    LANGUAGES CXX
)

docs_early_return()

include(cmake/project-is-top-level.cmake)
include(cmake/variables.cmake)

find_package(OpenGL REQUIRED)
find_package(glfw3 3.3 REQUIRED)

# ---- Declare library ----

add_library(
    learnopengl_lib OBJECT
    source/lib.cpp
)

target_include_directories(
    learnopengl_lib ${warning_guard}
    PUBLIC
    "$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/source>"
)

target_compile_features(learnopengl_lib PUBLIC cxx_std_20)

# ---- Declare executable ----

target_link_libraries(learnopengl_exe PRIVATE glfw)
target_link_libraries(learnopengl_exe PRIVATE OpenGL::GL)

set_target_properties(
    learnopengl_exe PROPERTIES
    OUTPUT_NAME learnopengl
    EXPORT_NAME exe
)

target_compile_features(learnopengl_exe PRIVATE cxx_std_20)

target_link_libraries(learnopengl_exe PRIVATE learnopengl_lib)
add_executable(learnopengl_exe source/main.cpp)
add_executable(learnopengl::exe ALIAS learnopengl_exe)

# ---- Install rules ----

if(NOT CMAKE_SKIP_INSTALL_RULES)
  include(cmake/install-rules.cmake)
endif()

# ---- Developer mode ----

if(NOT learnopengl_DEVELOPER_MODE)
  return()
elseif(NOT PROJECT_IS_TOP_LEVEL)
  message(
      AUTHOR_WARNING
      "Developer mode is intended for developers of learnopengl"
  )
endif()

include(cmake/dev-mode.cmake)

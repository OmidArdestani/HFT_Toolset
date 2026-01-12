# Minimum CMake version chosen for modern target features
cmake_minimum_required(VERSION 3.20)

project(HFT_Toolset VERSION 0.1.0 LANGUAGES CXX)

# Enforce modern C++ and keep flags consistent across consumers
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Static library target
add_library(hft_toolset STATIC src/library_anchor.cpp)
add_library(HFT::Toolset ALIAS hft_toolset)

target_include_directories(hft_toolset
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
        $<INSTALL_INTERFACE:include>
)

target_compile_features(hft_toolset PUBLIC cxx_std_23)

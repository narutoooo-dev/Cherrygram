# Colorado Library - Encryption/Decryption utilities for Telegram

cmake_minimum_required(VERSION 3.16)

# Set compilation flags
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -Wall -Oz -fno-strict-aliasing -ffunction-sections -fdata-sections")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Oz -fno-strict-aliasing -ffunction-sections -fdata-sections")

# Collect all source files in the colorado directory
file(GLOB COLORADO_SOURCES
    "${CMAKE_CURRENT_SOURCE_DIR}/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/*.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/*.cc"
    "${CMAKE_CURRENT_SOURCE_DIR}/*.s"
    "${CMAKE_CURRENT_SOURCE_DIR}/*.S"
)

# If no source files found, create an empty library to avoid errors
if(NOT COLORADO_SOURCES)
    message(WARNING "No source files found in colorado directory. Creating empty library to satisfy build.")
    
    # Create an empty source file
    file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/empty.cpp" 
        "// Empty file to satisfy CMake build\n"
        "void __colorado_empty_function() {}\n"
    )
    set(COLORADO_SOURCES "${CMAKE_CURRENT_BINARY_DIR}/empty.cpp")
endif()

# Create the colorado static library
add_library(colorado STATIC ${COLORADO_SOURCES})

# Add include directories
target_include_directories(colorado PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# Set compilation options
target_compile_options(colorado PRIVATE
    -Wall
    -Wextra
    -Oz
    -fno-strict-aliasing
    -fvisibility=hidden
    -ffunction-sections
    -fdata-sections
)

# Set target properties
set_target_properties(colorado PROPERTIES
    ANDROID_ARM_MODE arm
    CXX_STANDARD 14
    CXX_STANDARD_REQUIRED ON
)

# Link required system libraries
target_link_libraries(colorado
    log
    z
    c
    m
)

# If using BoringSSL or OpenSSL for crypto
# target_link_libraries(colorado
#     crypto
#     ssl
# )

# Add definitions if needed
target_compile_definitions(colorado PRIVATE
    COLORADO_EXPORTS
    ANDROID
)

# For debugging (optional)
# target_compile_options(colorado PRIVATE -DDEBUG)

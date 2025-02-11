
cmake_minimum_required(VERSION 3.16)
set(CMAKE_CXX_STANDARD          14) 
set(CMAKE_CXX_STANDARD_REQUIRED ON) 
set(CMAKE_CXX_EXTENSIONS        OFF) 

project(KeyGenDistributed_project)

if(CMAKE_SYSTEM_NAME STREQUAL "Windows")

    # Qrypt Security is built on Windows using the multi-threaded dll runtime library
    # Our executable must use the same runtime library.
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreadedDLL")

elseif(CMAKE_SYSTEM_NAME STREQUAL "Linux")

    # Enable -fPIC
    set(CMAKE_POSITION_INDEPENDENT_CODE ON)

endif()

# Specify whether to build EncryptTool
option(BUILD_ENCRYPT_TOOL "Build encryption tool" OFF)

if(BUILD_ENCRYPT_TOOL)
add_executable(EncryptTool
    src/encrypt.cpp
    src/common.cpp
)
target_link_libraries(EncryptTool
    crypto
)
endif()

add_executable(KeyGenDistributed
    src/main.cpp
    src/common.cpp
)

# Pull in Qrypt Security header files
if(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    target_compile_definitions(KeyGenDistributed PUBLIC MACOS_FRAMEWORK)
    target_include_directories(KeyGenDistributed PUBLIC
        "$ENV{HOME}/Library/Frameworks/QryptSecurity.framework/Headers"
    )
else()
    target_include_directories(KeyGenDistributed PUBLIC
        "lib/QryptSecurity/include"
    )
endif()

# Link against QryptSecurity dynamic library
if(CMAKE_SYSTEM_NAME STREQUAL "Windows")
    target_link_libraries(KeyGenDistributed
        "${CMAKE_CURRENT_LIST_DIR}/lib/QryptSecurity/lib/QryptSecurity.lib"
    )
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    target_link_libraries(KeyGenDistributed
       "$ENV{HOME}/Library/Frameworks/QryptSecurity.framework/QryptSecurity"
    )
else()
    target_link_libraries(KeyGenDistributed
        "${CMAKE_CURRENT_LIST_DIR}/lib/QryptSecurity/lib/libQryptSecurity.so"
        "${CMAKE_CURRENT_LIST_DIR}/lib/QryptSecurity/lib/libgf2x.so"
    )
endif()

# If on Windows, copy QryptSecurity.dll to the directory where the executable is built
# so that the executable can find it.
if(CMAKE_SYSTEM_NAME STREQUAL "Windows")
    add_custom_command(TARGET KeyGenDistributed POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_CURRENT_LIST_DIR}/lib/QryptSecurity/bin/QryptSecurity.dll
        $<TARGET_FILE_DIR:KeyGenDistributed>)
endif()

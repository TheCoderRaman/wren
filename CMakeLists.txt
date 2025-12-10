# vendror/wren/CMakeLists.txt

# Specify the minimum version of CMake required
cmake_minimum_required(VERSION 3.10.0)

# Define the library name
set(WREN_LIB_NAME wren)

# --- 1. Define Source Files for the Wren VM Core ---
# Find all .c files in the 'vm' subdirectory.
file(GLOB WREN_VM_SOURCES
    "src/vm/*.c"
)

# --- 2. Define Source Files for Optional Modules (Example: uses all .c files in 'optional') ---
# NOTE: You may need to refine this if you don't want all optional modules.
file(GLOB WREN_OPTIONAL_SOURCES
    "src/optional/*.c"
)

# Combine all source files
set(WREN_SOURCES
    ${WREN_VM_SOURCES}
    ${WREN_OPTIONAL_SOURCES}
)

# --- 3. Create the Static Library ---
# The library will be static so it can be linked into your main executable.
add_library(${WREN_LIB_NAME} STATIC ${WREN_SOURCES})

# --- Add Wren targets to the parent scope for WrenBind17's find_package ---
# When WrenBind17's CMake runs find_package(Wren), it needs to know the target name.
# By setting this variable, we allow the subsequent find_package to succeed.
set(WREN_FOUND TRUE CACHE BOOL "Wren library found (from custom build)" FORCE)
set(WREN_LIBRARIES wren CACHE STRING "The Wren library target name" FORCE)

# --- 4. Define Public Include Directory ---
# The 'include' directory contains the public headers (e.g., wren.h)
# which your main project needs to see.
target_include_directories(${WREN_LIB_NAME}
    PUBLIC
        "${CMAKE_CURRENT_SOURCE_DIR}/src/include"
)

# --- 5. Define Private/Internal Include Directory ---
# The 'vm' directory contains internal headers that the VM sources need to see.
target_include_directories(${WREN_LIB_NAME}
    PRIVATE
        "${CMAKE_CURRENT_SOURCE_DIR}/src/vm"
)

target_include_directories(${WREN_LIB_NAME}
    PRIVATE
        "${CMAKE_CURRENT_SOURCE_DIR}/src/optional"
)

# Set common compilation properties (Optional, but good practice for C/C++ libs)
target_compile_features(${WREN_LIB_NAME} PUBLIC c_std_99)

# Note: You can skip setting output directories here; the main project's CMake will handle it.
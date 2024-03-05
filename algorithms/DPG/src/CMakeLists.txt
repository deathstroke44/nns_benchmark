cmake_minimum_required(VERSION 3.0)
project(YourProjectName)

# Set the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set optimization level
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")

# Boost
find_package(Boost REQUIRED COMPONENTS timer chrono system program_options)
if (Boost_FOUND)
    include_directories(${Boost_INCLUDE_DIRS})
else()
    message(FATAL_ERROR "Boost dynamic-bitset is required")
endif()

# OpenMP
find_package(OpenMP REQUIRED)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
else()
    message(FATAL_ERROR "OpenMP is required")
endif()

# Source files
set(SOURCES
    DPG_index.cpp
    DPG_search.cpp
    DPG_diverse.cpp
    kgraph.cpp
    metric.cpp
    RandGen.cpp
)

# Headers
set(HEADERS
    kgraph.h
    kgraph-data.h
    RandGen.h
)

# Executables
foreach(source ${SOURCES})
    get_filename_component(name ${source} NAME_WE)
    add_executable(${name} ${source} ${HEADERS})
    target_link_libraries(${name} ${Boost_LIBRARIES} ${OpenMP_CXX_LIBRARIES} m rt)
endforeach(source)

# Set output directory for executables
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)

# Clean rule
add_custom_target(clean-all
    COMMAND ${CMAKE_BUILD_TOOL} clean
    COMMAND rm -f ${CMAKE_BINARY_DIR}/Makefile ${CMAKE_BINARY_DIR}/CMakeCache.txt
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)

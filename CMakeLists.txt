cmake_minimum_required(VERSION 3.14)

project(testFetchContentTileDB)

include(FetchContent)
set(CMAKE_CXX_STANDARD 17)

FetchContent_Declare(
        tiledb_super
        GIT_REPOSITORY https://github.com/TileDB-Inc/TileDB.git
        GIT_TAG 2.11.1
)
FetchContent_GetProperties(tiledb_super)
if(NOT tiledb_super_POPULATED)
    FetchContent_Populate(tiledb_super)

    set(TILEDB_VERBOSE ON CACHE INTERNAL "Enable verbose mode")
    set(TILEDB_S3 OFF CACHE INTERNAL "Enable S3 support")
    set(TILEDB_STATIC OFF CACHE INTERNAL "Enable static library building")
    set(TILEDB_SERIALIZATION OFF CACHE INTERNAL "Enable serialization support")
    set(TILEDB_FORCE_ALL_DEPS OFF CACHE INTERNAL "Make shared library self contained")
    set(TILEDB_TBB OFF CACHE INTERNAL "Disable TBB")
    set(TILEDB_NO_API_DEPRECATION_WARNINGS ON CACHE INTERNAL "Disable deprecated function call warnings inside of TileDB")

    message(STATUS "tiledb_super_SOURCE_DIR=${tiledb_super_SOURCE_DIR}")
    message(STATUS "tiledb_super_BINARY_DIR=${tiledb_super_BINARY_DIR}")
    add_subdirectory(${tiledb_super_SOURCE_DIR} ${tiledb_super_BINARY_DIR})


    SET(TILEDB_INCLUDE_DIR "${tiledb_super_BINARY_DIR}/tiledb/examples")
    SET(TILEDB_LIBRARIES "${tiledb_super_BINARY_DIR}/tiledb/tiledb/${CMAKE_SHARED_LIBRARY_PREFIX}tiledb${CMAKE_SHARED_LIBRARY_SUFFIX}")
    message(STATUS "TILEDB_LIBRARIES=${TILEDB_LIBRARIES}")
endif()

add_executable(main main.cpp)
add_dependencies(main tiledb)
target_link_libraries(main ${TILEDB_LIBRARIES})
target_include_directories(main PRIVATE ${TILEDB_INCLUDE_DIR})
target_compile_definitions(main PRIVATE TILEDB_NO_API_DEPRECATION_WARNINGS=1)

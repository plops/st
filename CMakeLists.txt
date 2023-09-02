cmake_minimum_required(VERSION 3.17)
project(st)
set(CMAKE_C_STANDARD 11)

set(
        SOURCE_FILES
        st.c
        x.c
)
option(ENABLE_ASAN "Enable AddressSanitizer" OFF)
if (ENABLE_ASAN)
    add_compile_options(-fsanitize=address)
    add_link_options(-fsanitize=address)
endif ()

find_package(PkgConfig REQUIRED)
pkg_check_modules(fontconfig REQUIRED)
pkg_check_modules(freetype2 REQUIRED)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

#https://interrupt.memfault.com/blog/best-and-worst-gcc-clang-compiler-flags

add_compile_options(
        -Wall -Wextra -Wconversion -Wdouble-promotion
        -Wno-unused-parameter -Wno-unused-function -Wno-sign-conversion
        -Wvla -Wframe-larger-than=5000 -Wstack-usage=10000
        -Wshadow -Wformat=2 -fno-common -fstack-usage
        -fvisibility=hidden
        -fno-strict-overflow -Wno-strict-overflow
)

# set compiler flags
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_options(
            -g -g3 -ggdb -gdwarf-4
    )
else ()
    add_compile_options(
            -Ofast
            -ffunction-sections -fdata-sections
            -Wpadded

    )
    add_link_options(-Wl,-gc-sections,--print-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)

endif ()

include_directories(${CMAKE_SOURCE_DIR})
add_executable(
        ${PROJECT_NAME}
        ${SOURCE_FILES}
)

target_link_libraries(
        ${PROJECT_NAME}

)
target_include_directories(${PROJECT_NAME} PUBLIC ${fontconfig_INCLUDE_DIRS} ${freetype2_INCLUDE_DIRS} /usr/include/freetype2)

target_compile_definitions(${PROJECT_NAME} PUBLIC VERSION="0.9")

# Link libraries
target_link_libraries(${PROJECT_NAME}
        #${fontconfig_LIBRARIES}
        #${freetype2_LIBRARIES}
        freetype
        fontconfig
        m
        rt
        X11
        util
        Xft
)
 

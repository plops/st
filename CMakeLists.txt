cmake_minimum_required(VERSION 3.17)
project(st)
set(CMAKE_CXX_STANDARD 20)

set(
        SOURCE_FILES
        st.c
	x.c
)

find_package(PkgConfig REQUIRED)
pkg_check_modules(fontconfig REQUIRED)
pkg_check_modules(freetype2 REQUIRED)

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
                   -fsanitize=undefined
    )
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fsanitize=undefined")
elsif()
    add_compile_options(
            -ffunction-sections -fdata-sections
	    -Wpadded
	    
    )
    add_link_options(
             --gc-sections --print-gc-sections
    )

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

# Compiler options
target_compile_options(${PROJECT_NAME} PUBLIC "-DVERSION=\"0.9\"")

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
 

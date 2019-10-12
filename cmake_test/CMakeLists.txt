cmake_minimum_required (VERSION 2.8.12)

MACRO(HEADER_DIRECTORIES return_list)
    FILE(GLOB_RECURSE new_list *.h)
    SET(dir_list "")
    FOREACH(file_path ${new_list})
        GET_FILENAME_COMPONENT(dir_path ${file_path} PATH)
        SET(dir_list ${dir_list} ${dir_path})
    ENDFOREACH()
    LIST(REMOVE_DUPLICATES dir_list)
    SET(${return_list} ${dir_list})
ENDMACRO()

# set the build type
if(NOT CMAKE_BUILD_TYPE) 
    set(CMAKE_BUILD_TYPE Release)
endif(NOT CMAKE_BUILD_TYPE)

if(CMAKE_BUILD_TYPE MATCHES Debug)
  message("Debug build.")
elseif(CMAKE_BUILD_TYPE MATCHES Release)
  message("Release build.")
endif()

# set search path for CMake modules
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/CMake/Modules)

# include OpenOCD module, but make it not required
find_package(Openocd)

# Project specific settings
set(application_name "cmake_test")

project (${application_name} C CXX ASM)

# STM32-specific settings
set(DEVICE_FAMILY STM32F303x8)

# Speed of external resonator
set(HSE_VALUE 8000000)

# Used linker file (just for testing)
get_filename_component(LINKER_SCRIPT STM32F303K8_FLASH.ld ABSOLUTE)

# Use LTO? WARNING: This might break the code
#set(ADDITIONAL_CORE_FLAGS -flto)

# OpenOCD config file
get_filename_component(OPENOCD_CONFIG tools/stm32f4discovery.cfg ABSOLUTE)

file(GLOB_RECURSE sources
  *.c
  *.cpp
  *.s
  )
#message(${sources})

HEADER_DIRECTORIES(includes)
#message(${includes})

add_subdirectory(Src)
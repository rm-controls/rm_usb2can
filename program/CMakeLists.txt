#THIS FILE IS AUTO GENERATED FROM THE TEMPLATE! DO NOT CHANGE!
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
cmake_minimum_required(VERSION 3.22)

# specify cross-compilers and tools
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_AR arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# project settings
project(candleLight C ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 11)

#Uncomment for software floating point
add_compile_options(-mfloat-abi=soft)
add_compile_options(-mcpu=cortex-m0 -mthumb -mthumb-interwork -Wno-unused-parameter)
add_compile_options(
        -std=gnu11 -mthumb
        -Wall -Wextra -Werror
        -fmessage-length=0
        -fsigned-char
        -ffunction-sections -fdata-sections
        -ffreestanding
        -fno-move-loop-invariants
        -Ofast
        -flto -ffat-lto-objects
        --specs=nano.specs
        --specs=nosys.specs
)

# Enable assembler files preprocessing
add_compile_options($<$<COMPILE_LANGUAGE:ASM>:-x$<SEMICOLON>assembler-with-cpp>)

add_custom_target(version_h BYPRODUCTS "${CMAKE_CURRENT_BINARY_DIR}/version.h"
        COMMAND ${CMAKE_COMMAND}
        -D SRCDIR= "${CMAKE_CURRENT_SOURCE_DIR}"
        -P ${CMAKE_CURRENT_SOURCE_DIR}/gitversion.cmake)

include_directories(library/include system user/include system/cmsis usb ${CMAKE_CURRENT_BINARY_DIR})

add_definitions(-DUSE_HAL_DRIVER -DSTM32F072xB)

file(GLOB_RECURSE SOURCES "user/source/*.c" "library/source/*.c" "usb/*.c" "system/*.c")

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/STM32F072XB.ld)

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
add_link_options(-mcpu=cortex-m0 -mthumb -mthumb-interwork)
add_link_options(
        -mthumb -Wall -Wextra -g3
        -Xlinker --gc-sections
        --specs=nano.specs
        --specs=nosys.specs
)
add_link_options(-T ${LINKER_SCRIPT})
add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})
add_dependencies(${PROJECT_NAME}.elf version_h)

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")

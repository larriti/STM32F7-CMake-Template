# CMake 最低版本号要求
cmake_minimum_required (VERSION 3.1)

# 项目信息
project( Template )
enable_language(ASM)

# 设置输出路径
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)

# STDLIB
# add_subdirectory (Libraries)
set (CMAKE_SHARED_LIBRARY_LINK_C_FLAGS)

# 包含的头文件
include_directories (Drivers/CMSIS/Device/Include )
include_directories (Drivers/CMSIS/Include )
include_directories (Drivers/STM32F7xx_HAL_Driver/Inc)
include_directories (User/Include)


# 需要编译的文件
aux_source_directory (User/Source/ DIR_USER_SRCS)
aux_source_directory (Drivers/CMSIS/Device/Source DIR_CMSIS_SRCS)

# CC AR LD AS
set (CMAKE_C_COMPILER "arm-none-eabi-gcc")
set (CMAKE_C_AR "arm-none-eabi-ar")
set (CMAKE_ASM_COMPILER "arm-none-eabi-gcc")

# CFLAGS
set (CMAKE_C_FLAGS "-mthumb -fno-builtin -mcpu=cortex-m7 -mfpu=fpv5-sp-d16 -mfloat-abi=softfp -Wall -std=gnu99 -ffunction-sections -fdata-sections -fomit-frame-pointer -mabi=aapcs -fno-unroll-loops -ffast-math -ftree-vectorize" CACHE INTERNAL "c compiler flags")
set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D USE_HAL_DRIVER -D STM32F746xx")

# CXXFLAGS
set (CMAKE_CXX_FLAGS "-mthumb -fno-builtin -mcpu=cortex-m7 -mfpu=fpv5-sp-d16 -mfloat-abi=softfp -Wall -std=c++11 -ffunction-sections -fdata-sections -fomit-frame-pointer -mabi=aapcs -fno-unroll-loops -ffast-math -ftree-vectorize" CACHE INTERNAL "cxx compiler flags")

# ASMFLAGS
set (CMAKE_ASM_FLAGS "-mthumb -mcpu=cortex-m7 -mfpu=fpv5-sp-d16 -mfloat-abi=softfp -x assembler-with-cpp" CACHE INTERNAL "asm compiler flags")

# LDFLAGS
set (CMAKE_EXE_LINKER_FLAGS "-Wl,--gc-sections -mthumb -mcpu=cortex-m7 -mfpu=fpv5-sp-d16 -mfloat-abi=softfp -mabi=aapcs" CACHE INTERNAL "executable linker flags")
set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -T ${CMAKE_SOURCE_DIR}/ldscripts/STM32F746NGHx_FLASH.ld")

# 导入外部静态链接文件
add_library (STM32F7xx_HAL_Driver STATIC IMPORTED)
set_property (TARGET STM32F7xx_HAL_Driver PROPERTY IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/Drivers/Build/lib/libSTM32F7xx_HAL_Driver.a)

#指定生成目标
add_executable (${CMAKE_PROJECT_NAME}.elf ${DIR_USER_SRCS} ${DIR_CMSIS_SRCS} ${CMAKE_SOURCE_DIR}/Drivers/CMSIS/Device/Source/startup/startup_stm32f746xx.s)

# 链接静态库
target_link_libraries (${CMAKE_PROJECT_NAME}.elf STM32F7xx_HAL_Driver)

#生成二进制文件
add_custom_target(${CMAKE_PROJECT_NAME}.bin ALL arm-none-eabi-objcopy -Obinary "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf" "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.bin" DEPENDS ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf)
add_custom_target (size ALL arm-none-eabi-size "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf" DEPENDS ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf)

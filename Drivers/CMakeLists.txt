# CMake 最低版本要求

cmake_minimum_required (VERSION 3.1)

# 项目信息
project (STM32F7xx_HAL_DriverLib)

# 设置输出路径
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)

# 添加头文件路径
include_directories( CMSIS/Include )
include_directories( CMSIS/Device/Include )
include_directories( STM32F7xx_HAL_Driver/Inc )

# 查找依赖的所有源文件

# 查找需要编译的所有源文件
# 并将名称保存到 DIR_SRCS 变量中
aux_source_directory (STM32F7xx_HAL_Driver/Src DIR_STM32F7xx_HAL_Driver_SRCS)

# CC AR
set(CMAKE_C_COMPILER "arm-none-eabi-gcc")
set(CMAKE_C_AR "arm-none-eabi-ar")

# CFLAGS
set (CMAKE_C_FLAGS "-mthumb -fno-builtin -mcpu=cortex-m7 -mfpu=fpv5-sp-d16 -mfloat-abi=softfp -Wall -std=gnu99 -ffunction-sections -fdata-sections -fomit-frame-pointer -mabi=aapcs -fno-unroll-loops -ffast-math -ftree-vectorize" CACHE INTERNAL "c compiler flags")
set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DSTM32F746xx -DUSE_HAL_DRIVER")

# 生成静态链接库
add_library (STM32F7xx_HAL_Driver ${DIR_STM32F7xx_HAL_Driver_SRCS})

cmake_minimum_required(VERSION 3.13)
include(pico-sdk/pico_sdk_init.cmake)

project(firmware)
pico_sdk_init()

add_executable(firmware
  main.c
  msc_disk.c
  usb_descriptors.c
  mongoose.c
  net.c
  packed_fs.c
  pico-sdk/lib/tinyusb/lib/networking/rndis_reports.c)

target_include_directories(firmware PUBLIC
  .
  pico-sdk/lib/tinyusb/lib/networking)

target_link_libraries(firmware pico_stdlib pico_rand hardware_spi tinyusb_device)
pico_add_extra_outputs(firmware)     # create map/bin/hex file etc.

pico_enable_stdio_usb(firmware 0)    # Route stdio
pico_enable_stdio_uart(firmware 1)   # to the UART

# Mongoose build flags in mongoose_config.h

# Example build options
add_definitions(-DHTTP_URL="http://0.0.0.0/")

# Extra build flags (enable if needed)
add_definitions(-DDUAL_CONFIG=0)

# Prevent extreme optimization from breaking OTA
target_compile_options(firmware PRIVATE -fno-tree-loop-distribute-patterns)

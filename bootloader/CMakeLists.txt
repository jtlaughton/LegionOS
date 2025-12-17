set(BOOTLOADER_SOURCES
    src/main.c)

add_executable(bootloader ${BOOTLOADER_SOURCES})

target_include_directories(bootloader PRIVATE
    include)

set_target_properties(bootloader PROPERTIES
    LINK_FLAGS "-T ${CMAKE_CURRENT_SOURCE_DIR}/bootloader.ld -nostdlib")

add_custom_command(TARGET bootloader POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} -O binary bootloader bootloader.bin
    COMMENT "Creating bootloader binary for OpenFirmware")
cmake_minimum_required(VERSION 3.16)
project(LEGION ASM C)

set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/toolchain-powerpc.cmake)

enable_language(ASM)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mcpu=750 -fno-stack-protector -fno-pic")
set(CMAKE_ASM_FLAGS "${CMAKE_ASM_FLAGS} -mcpu=750")

add_subdirectory(bootloader)

add_custom_target(boot_image
    COMMAND ${CMAKE_SOURCE_DIR}/tools/mkimage.sh
    DEPENDS bootloader
    COMMENT "Creating bootable disk image"
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})

add_custom_target(run
    COMMAND qemu-system-ppc -M mac99 -cpu G4 -m 512
    -drive file=boot.img,format=raw,media=disk
    -serial stdio -display none -no-reboot
    -prom-env "boot-device=hd:,\\\\:tbxi"
    DEPENDS boot_image
    COMMENT "Running LegionOS in QEMU (Mac G4)")

add_custom_target(clean_all
    COMMAND ${CMAKE_BUILD_TOOL} clean
    COMMAND rm -f boot.img
    COMMENT "Clean all build artifacts and images")
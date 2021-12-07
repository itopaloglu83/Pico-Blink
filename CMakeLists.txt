cmake_minimum_required(VERSION 3.13)

# initialize the SDK based on PICO_SDK_PATH
# note: this must happen before project()
include(pico_sdk_import.cmake)

project(
    blink 
    VERSION 0.0.1 
    LANGUAGES C CXX ASM
)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# initialize the Raspberry Pi Pico SDK
pico_sdk_init()

# rest of your project
add_executable(
    ${PROJECT_NAME}
    blink.c
)

# Add pico_stdlib library which aggregates commonly used features
target_link_libraries(
    ${PROJECT_NAME} 
    pico_stdlib
)

# create map/bin/hex/uf2 file in addition to ELF.
pico_add_extra_outputs(${PROJECT_NAME})

# Use OpenOCD to burn the elf file.
# openocd -f interface/picoprobe.cfg -f target/rp2040.cfg -c "program blink.elf verify reset exit"

add_custom_target(
    flash
    DEPENDS ${PROJECT_NAME}
    COMMENT "Uploading sketch to Raspberry Pi Pico."
    COMMAND openocd -f interface/picoprobe.cfg -f target/rp2040.cfg -c "program ${PROJECT_NAME}.elf verify reset exit" -l /dev/null && echo "Done: Sketch uploaded successfully." || echo "Error: Upload failed."
    VERBATIM
)

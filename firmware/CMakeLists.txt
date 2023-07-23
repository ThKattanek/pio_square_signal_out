cmake_minimum_required(VERSION 3.12)

# Include build functions from Pico SDK
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)

project(pio_square_signal_out C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Creates a pico-sdk subdirectory in our project for the libraries
pico_sdk_init()

# Tell CMake where to find the executable source file
add_executable(${PROJECT_NAME}
    main.c
    square_out.pio
)

pico_generate_pio_header(${PROJECT_NAME} ${CMAKE_CURRENT_LIST_DIR}/square_out.pio)

file(MAKE_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/generated)

add_custom_target(pio_squarewave_datasheet DEPENDS
		${CMAKE_CURRENT_LIST_DIR}/generated/square_out.pio.h
		)

add_custom_command(OUTPUT ${CMAKE_CURRENT_LIST_DIR}/generated/square_out.pio.h
		DEPENDS ${CMAKE_CURRENT_LIST_DIR}/square_out.pio
		COMMAND Pioasm ${CMAKE_CURRENT_LIST_DIR}/square_out.pio ${CMAKE_CURRENT_LIST_DIR}/generated/square_out.pio.h
		VERBATIM)

# Create map/bin/hex/uf2 files
pico_add_extra_outputs(${PROJECT_NAME})

# Make the stdio functions use USB rather than the serial pins
pico_enable_stdio_usb(pio_square_signal_out 0)
pico_enable_stdio_uart(pio_square_signal_out 1)

# Pull in our pico_stdlib which pulls in commonly used features
target_link_libraries(${PROJECT_NAME} PRIVATE pico_stdlib hardware_pio)

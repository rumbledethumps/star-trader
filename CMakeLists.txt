cmake_minimum_required(VERSION 3.21)

include(${CMAKE_CURRENT_LIST_DIR}/tools/rp6502.cmake)

project(startrader BASIC)

add_executable(startrader)
rp6502_asset(startrader help src/help.txt)
rp6502_asset(startrader loader.bas src/loader.bas)
rp6502_asset(startrader startrader.bas src/startrader.bas)
rp6502_basic(startrader loader.bas)
rp6502_web(startrader CONFIG [[
    title: 'Star Trader',
    border: '1em',
    footer: 'Type a command letter, then press Enter.',
    overlay: '',
]])

# The play test runs the ROM with no arguments, so autorun.bas starts it.
enable_testing()
find_package(Python3 REQUIRED COMPONENTS Interpreter)
add_test(NAME play
    COMMAND ${Python3_EXECUTABLE} ${RP6502_TOOLS_DIR}/rp6502.py
        -c ${CMAKE_CURRENT_SOURCE_DIR}/.rp6502 execute
        --script ${CMAKE_CURRENT_SOURCE_DIR}/tests/play.txt --seed 1
        ${CMAKE_CURRENT_BINARY_DIR}/startrader.rp6502
)
set_tests_properties(play PROPERTIES TIMEOUT 120)

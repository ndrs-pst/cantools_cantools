cmake_minimum_required(VERSION 3.15)
project(cantoolsTest C)
set(CMAKE_C_STANDARD 99)

# Regenerate C files
execute_process(COMMAND cantools generate_c_source ../files/dbc/motohawk.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/padding_bit_order.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/vehicle.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/open_actuator.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/multiplex.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/multiplex_2.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/floating_point.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/no_signals.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/choices.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/signed.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/sym/min-max-only-6.0.sym 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source ../files/dbc/abs.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source --database-name my_database_name ../files/dbc/motohawk.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source --database-name motohawk_bit_fields --bit-fields ../files/dbc/motohawk.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source --database-name floating_point_bit_fields --bit-fields ../files/dbc/floating_point.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source --database-name signed_bit_fields --bit-fields ../files/dbc/signed.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
execute_process(COMMAND cantools generate_c_source --database-name open_actuator_node_sensor --node Sensor ../files/dbc/open_actuator.dbc 
                WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})

# fetch unity test framework
add_subdirectory(test_framework)

# Add source code generated by cantools
set(SOURCES
    motohawk.c
    padding_bit_order.c
    vehicle.c
    open_actuator.c
    multiplex.c
    multiplex_2.c
    floating_point.c
    no_signals.c
    signed.c
    min_max_only_6_0.c
    abs.c
    my_database_name.c
    motohawk_bit_fields.c
    floating_point_bit_fields.c
    signed_bit_fields.c
    open_actuator_node_sensor.c)
add_library(cantoolsGenCode ${SOURCES})

# Enable all warnings for the generated source code
if (MSVC)
    target_compile_options(cantoolsGenCode PRIVATE /W4 /WX)
else()
    target_compile_options(cantoolsGenCode PRIVATE -Wall -Wextra -Wpedantic -Werror)
endif()

# Add the test executable
set(TEST_SOURCES
    test_runner.c
    test_basic.c
    test_bit_fields.c
    test_initial_values.c
)
add_executable(test_runner ${TEST_SOURCES})
target_include_directories(test_runner PRIVATE ${unity_SOURCE_DIR}/include)
target_compile_definitions(unity PUBLIC -DUNITY_INCLUDE_DOUBLE)
target_link_libraries(test_runner unity cantoolsGenCode)

# Add a custom target to run the tests
add_custom_target(run_tests
    COMMAND test_runner
    DEPENDS test_runner
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)
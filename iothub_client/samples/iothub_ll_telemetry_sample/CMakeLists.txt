#Copyright (c) Microsoft. All rights reserved.
#Licensed under the MIT license. See LICENSE file in the project root for full license information.

#this is CMakeLists.txt for iothub_client_sample_mqtt

if(NOT ${use_mqtt} AND NOT ${use_amqp} AND NOT ${use_http})
    message(FATAL_ERROR "iothub_ll_telemetry_sample being generated without protocol support")
endif()

compileAsC99()

set(iothub_c_files
    iothub_ll_telemetry_sample.c
    ${PROJECT_SOURCE_DIR}/certs/certs.c
)

IF(WIN32)
    #windows needs this define
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
ENDIF(WIN32)

include_directories(${PROJECT_SOURCE_DIR}/certs)

include_directories(.)

add_executable(iothub_ll_telemetry_sample ${iothub_c_files})
target_link_libraries(iothub_ll_telemetry_sample iothub_client)

if(${use_http})
    target_link_libraries(iothub_ll_telemetry_sample iothub_client_http_transport)
    add_definitions(-DUSE_HTTP)
endif()

if(${use_amqp})
    target_link_libraries(iothub_ll_telemetry_sample iothub_client_amqp_transport iothub_client_amqp_ws_transport)
    linkUAMQP(iothub_ll_telemetry_sample)
    add_definitions(-DUSE_AMQP)
endif()

if(${use_mqtt})
    target_link_libraries(iothub_ll_telemetry_sample iothub_client_mqtt_transport iothub_client_mqtt_ws_transport)
    linkMqttLibrary(iothub_ll_telemetry_sample)
    add_definitions(-DUSE_MQTT)
endif()

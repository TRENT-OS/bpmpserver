#
# SystemController
#
# Copyright (C) 2022-2024, HENSOLDT Cyber GmbH
#
# SPDX-License-Identifier: GPL-2.0-or-later
#
# For commercial licensing, contact: info.cyber@hensoldt.net
#

cmake_minimum_required(VERSION 3.8.2)

if(SDK_USE_CAMKES)

    os_sdk_import_from_global_components(
        "components/modules/fdt-bind-driver"
    )

endif()  # SDK_USE_CAMKES

function(BPMPServer_DeclareCAmkESComponent
    name
)

    if(KernelArchARM)
        set(CAmkESDTS ON CACHE BOOL "" FORCE)
    endif()

    # running CAMkES files though the C pre processor should be enabled by
    # default in the seL4/CAmkES build system and the SDK also enables this,
    # but just to play safe we ensure once more here that it is enabled.
    set(CAmkESCPP ON CACHE BOOL "" FORCE)

    CAmkESAddImportPath(
        "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/include/plat/${KernelPlatform}"
    )

    CAmkESAddCPPInclude(
        "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/include/plat/${KernelPlatform}"
    )

    CAmkESAddImportPath(
        "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/camkes-include"
    )

    CAmkESAddCPPInclude(
        "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/camkes-include"
    )

    DeclareCAmkESComponent(
        ${name}
        SOURCES
            ${CMAKE_CURRENT_FUNCTION_LIST_DIR}/src/server.c
        INCLUDES
            ${CMAKE_CURRENT_FUNCTION_LIST_DIR}/include/plat/${KernelPlatform}
        C_FLAGS
            -Wall
            -Werror
        LIBS
            os_core_api
            lib_debug
            lib_macros
            platsupportports
    )

endfunction()
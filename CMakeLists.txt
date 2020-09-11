#
# Copyright 2020, Data61, CSIRO (ABN 41 687 119 230)
#
# SPDX-License-Identifier: BSD-2-Clause
#

cmake_minimum_required(VERSION 3.13.0)

include(settings.cmake)

project(commonroot-demo C CXX ASM)

find_package(seL4 REQUIRED)
find_package(elfloader-tool REQUIRED)
find_package(util_libs REQUIRED)
find_package(sel4commonroot REQUIRED)

# Set the roottask CNode size to be large enough for DTB, timer caps, etc
# Note that this works for the platforms that we have at the moment, and may
# need to be increased in the future
set(KernelRootCNodeSizeBits 13 CACHE INTERNAL "")

sel4_import_kernel()

if((NOT AllowSettingsOverride) AND (KernelArchARM OR KernelArchRiscV))
    # Elfloader settings that correspond to how Data61 sets its boards up.
    ApplyData61ElfLoaderSettings(${KernelPlatform} ${KernelSel4Arch})
endif()
elfloader_import_project()

include(simulation)
if(KernelSel4ArchX86_64)
    SetSimulationScriptProperty(MEM_SIZE "3G")
endif()
if(KernelPlatformQEMUArmVirt)
    SetSimulationScriptProperty(MEM_SIZE "2G")
endif()

GenerateSimulateScript()

sel4commonroot_setup_system()
util_libs_import_libraries()

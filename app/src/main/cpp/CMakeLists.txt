# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.4.1)

#---------------------------------------------------------------------
#-------------------------   Dobby   ---------------------------------
#---------------------------------------------------------------------

set(DobbyHome D:/app/DobbyTest/app/src/main/cpp/Dobby)
include_directories(
        ${DobbyHome}/include
        ${DobbyHome}/builtin-plugin
        ${DobbyHome}/builtin-plugin/SymbolResolver
        ${DobbyHome}/builtin-plugin/AndroidRestriction
        ${DobbyHome}/builtin-plugin/ApplicationEventMonitor
        ${DobbyHome}/external/logging
        ${DobbyHome}/builtin-plugin/SymbolResolver/macho
)

add_library(
        FirebaseCppSocket
        SHARED
#        ${DobbyHome}/builtin-plugin/AndroidRestriction/android_restriction_demo.cc
#        ${DobbyHome}/builtin-plugin/ApplicationEventMonitor/posix_file_descriptor_operation_monitor.cc
#        ${DobbyHome}/builtin-plugin/ApplicationEventMonitor/dynamic_loader_monitor.cc
        Tools/tools.cpp
        inject_dobby.cpp)

target_link_libraries(
        FirebaseCppSocket
        dobby
        ${log-lib})

#---------------------------------------------------------------------
#-------------------------   InlineHook   ----------------------------
#---------------------------------------------------------------------

add_library(inject_inlinehook
        SHARED
        inject_inlinehook.cpp
        Tools/tools.cpp
        inlineHook/inlineHook.c
        inlineHook/relocate.c)

target_link_libraries(inject_inlinehook
        android
        log)

#---------------------------------------------------------------------
#-------------------------   Inject_call_sa10   ----------------------
#---------------------------------------------------------------------

add_library(inject1
        SHARED
        inject_test.cpp
        Tools/tools.cpp
        inlineHook/inlineHook.c
        inlineHook/relocate.c)

target_link_libraries(inject1
        android
        log)

add_library(inject
        SHARED
        inject_test_dobby.cpp
        Tools/tools.cpp
        inlineHook/inlineHook.c
        inlineHook/relocate.c)

target_link_libraries(inject
        android
        dobby
        log)


#---------------------------------------------------------------------
#-------------------------   native-lib   ----------------------
#---------------------------------------------------------------------

add_library(native-lib
        SHARED
        Tools/tools.cpp
        native-lib.cpp)

target_link_libraries(native-lib
        android
        log)
#---------------------------------------------------------------------

find_library(
        log-lib
        log)

macro(SET_OPTION option value)
    set(${option} ${value} CACHE INTERNAL "" FORCE)
endmacro()
SET_OPTION(DOBBY_DEBUG ON)
SET_OPTION(DOBBY_GENERATE_SHARED OFF)
SET_OPTION(DynamicBinaryInstrument ON)
SET_OPTION(NearBranch ON)
SET_OPTION(Plugin.SymbolResolver ON)
add_subdirectory(${DobbyHome} dobby)
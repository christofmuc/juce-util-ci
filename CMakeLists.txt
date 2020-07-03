#
#  Copyright (c) 2019 Christof Ruch. All rights reserved.
#
#  Dual licensed: Distributed under Affero GPL license by default, an MIT license is available for purchase
#

cmake_minimum_required(VERSION 3.14)

project(juce-utils-ui VERSION 1.0.0)

# We need a little bit support for the JUCE library, and also want to put our own CMake helpers on the module path
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

# Since JUCE 6.0.0, we can add juce as a CMake module directly
add_subdirectory(third_party/JUCE)

# Select which JUCE modules our little program needs
set(JUCE_MODULES_NEEDED juce::juce_core juce::juce_data_structures juce::juce_audio_basics juce::juce_audio_devices juce::juce_audio_formats)

# Hacking together a fake top level project that I will use to have the Juce CMake system generate the proper headers and includes
juce_add_console_app(toplevel PRODUCT_NAME "fake")
target_sources(toplevel PRIVATE main.cpp)
target_link_libraries(toplevel PRIVATE ${JUCE_MODULES_NEEDED})

# Make an old school JuceHeader.h which we will include in all subprojects
juce_generate_juce_header(toplevel)

# Now make sure all subprojects will get those include directories automatically
include_directories(BEFORE ${CMAKE_CURRENT_BINARY_DIR}/toplevel_artefacts/JuceLibraryCode ${JUCE_MODULES_DIR})

# Not sure how to use this properly - how is the JUCE configured?
set(COMMON_JUCE_DEFS ${CMAKE_CURRENT_BINARY_DIR}/toplevel_artefacts/JuceLibraryCode/Debug/Defs.txt)

# Add my own source that relies on the setup above
add_subdirectory(Source)

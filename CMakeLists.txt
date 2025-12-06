cmake_minimum_required(VERSION 3.15)

project(vital VERSION 1.0.6)

# Globally disable authentication for open-source build
add_compile_definitions(NO_AUTH=1)

# Dependencies
find_package(Freetype REQUIRED)
if(UNIX AND NOT APPLE)
    find_package(X11 REQUIRED)
    find_package(ALSA REQUIRED)
endif()
find_package(CURL REQUIRED)
find_package(OpenGL REQUIRED)

# Define the standalone application
add_executable(vital WIN32)

# Include directories
target_include_directories(vital PRIVATE
    standalone/JuceLibraryCode
    third_party/JUCE/modules
    src/common
    src/common/wavetable
    src/interface/editor_components
    src/interface/editor_sections
    src/interface/look_and_feel
    src/interface/wavetable
    src/interface/wavetable/editors
    src/interface/wavetable/overlays
    src/standalone
    src/synthesis/synth_engine
    src/synthesis/effects
    src/synthesis/filters
    src/synthesis/framework
    src/synthesis/lookups
    src/synthesis/modulators
    src/synthesis/modules
    src/synthesis/producers
    src/synthesis/utilities
    third_party
)

# JUCE Sources (from JuceLibraryCode)
target_sources(vital PRIVATE
    standalone/JuceLibraryCode/include_juce_audio_basics.cpp
    standalone/JuceLibraryCode/include_juce_audio_devices.cpp
    standalone/JuceLibraryCode/include_juce_audio_formats.cpp
    standalone/JuceLibraryCode/include_juce_audio_processors.cpp
    standalone/JuceLibraryCode/include_juce_audio_utils.cpp
    standalone/JuceLibraryCode/include_juce_core.cpp
    standalone/JuceLibraryCode/include_juce_data_structures.cpp
    standalone/JuceLibraryCode/include_juce_dsp.cpp
    standalone/JuceLibraryCode/include_juce_events.cpp
    standalone/JuceLibraryCode/include_juce_graphics.cpp
    standalone/JuceLibraryCode/include_juce_gui_basics.cpp
    standalone/JuceLibraryCode/include_juce_gui_extra.cpp
    standalone/JuceLibraryCode/include_juce_opengl.cpp
    standalone/JuceLibraryCode/BinaryData.cpp
)

# Project Sources
target_sources(vital PRIVATE
    src/unity_build/common.cpp
    src/unity_build/interface_editor_components.cpp
    src/unity_build/interface_editor_sections.cpp
    src/unity_build/interface_editor_sections2.cpp
    src/unity_build/interface_look_and_feel.cpp
    src/unity_build/interface_wavetable.cpp
    src/unity_build/standalone.cpp
    src/unity_build/synthesis.cpp

    # standalone.cpp includes main.cpp etc, but we might need to compile them separately if unity build is just a wrapper?
    # Checking src/unity_build/standalone.cpp again... it includes main.cpp.
    # So compiling src/unity_build/standalone.cpp is enough for the app entry point.
)

# Link system libraries
target_link_libraries(vital PRIVATE
    Freetype::Freetype
    CURL::libcurl
    OpenGL::GL
)

if(UNIX AND NOT APPLE)
    target_link_libraries(vital PRIVATE
        X11::X11
        ALSA::ALSA
        dl
        pthread
        rt
    )
endif()

# Compile definitions
target_compile_definitions(vital PRIVATE
    JUCE_OPENGL3=1
    NO_AUTH=1
    JUCE_DSP_USE_SHARED_FFTW=1
    JUCE_WEB_BROWSER=0
    JUCE_USE_CURL=1
)

if(UNIX AND NOT APPLE)
    target_compile_definitions(vital PRIVATE
        JUCE_JACK_CLIENT_NAME="Vital"
        JUCE_ALSA_MIDI_INPUT_NAME="Vital"
        JUCE_ALSA_MIDI_OUTPUT_NAME="Vital"
        JUCE_USE_XRANDR=0
        LINUX=1
        JUCE_LINUX=1
    )
endif()

# Enable C++14
set_property(TARGET vital PROPERTY CXX_STANDARD 14)

# Tests
# add_executable(vital_tests)

# target_include_directories(vital_tests PRIVATE
#     tests/JuceLibraryCode
#     tests/synthesis
#     tests/interface
#     tests/stress
#     third_party/JUCE/modules
#     src/common
#     src/common/wavetable
#     src/interface/editor_components
#     src/interface/editor_sections
#     src/interface/look_and_feel
#     src/interface/wavetable
#     src/interface/wavetable/editors
#     src/interface/wavetable/overlays
#     src/standalone
#     src/synthesis/synth_engine
#     src/synthesis/effects
#     src/synthesis/filters
#     src/synthesis/framework
#     src/synthesis/lookups
#     src/synthesis/modulators
#     src/synthesis/modules
#     src/synthesis/producers
#     src/synthesis/utilities
#     third_party
# )

# target_sources(vital_tests PRIVATE
#     tests/main.cpp
#     tests/interface_tests.cpp
#     tests/stress_tests.cpp
#     tests/synthesis_tests.cpp

#     # We need to compile the project sources for the tests too
#     src/unity_build/common.cpp
#     src/unity_build/interface_editor_components.cpp
#     src/unity_build/interface_editor_sections.cpp
#     src/unity_build/interface_editor_sections2.cpp
#     src/unity_build/interface_look_and_feel.cpp
#     src/unity_build/interface_wavetable.cpp
#     src/unity_build/synthesis.cpp

#     # JUCE sources
#     standalone/JuceLibraryCode/include_juce_audio_basics.cpp
#     standalone/JuceLibraryCode/include_juce_audio_devices.cpp
#     standalone/JuceLibraryCode/include_juce_audio_formats.cpp
#     standalone/JuceLibraryCode/include_juce_audio_processors.cpp
#     standalone/JuceLibraryCode/include_juce_audio_utils.cpp
#     standalone/JuceLibraryCode/include_juce_core.cpp
#     standalone/JuceLibraryCode/include_juce_data_structures.cpp
#     standalone/JuceLibraryCode/include_juce_dsp.cpp
#     standalone/JuceLibraryCode/include_juce_events.cpp
#     standalone/JuceLibraryCode/include_juce_graphics.cpp
#     standalone/JuceLibraryCode/include_juce_gui_basics.cpp
#     standalone/JuceLibraryCode/include_juce_gui_extra.cpp
#     standalone/JuceLibraryCode/include_juce_opengl.cpp
#     standalone/JuceLibraryCode/BinaryData.cpp
# )

# target_link_libraries(vital_tests PRIVATE
#     Freetype::Freetype
#     CURL::libcurl
#     OpenGL::GL
# )

# if(UNIX AND NOT APPLE)
#     target_link_libraries(vital_tests PRIVATE
#         X11::X11
#         ALSA::ALSA
#         dl
#         pthread
#         rt
#     )
# endif()

# target_compile_definitions(vital_tests PRIVATE
#     JUCE_OPENGL3=1
#     REQUIRE_AUTH=1
#     JUCE_DSP_USE_SHARED_FFTW=1
#     JUCE_WEB_BROWSER=0
#     JUCE_USE_CURL=1
# )

# if(UNIX AND NOT APPLE)
#     target_compile_definitions(vital_tests PRIVATE
#         JUCE_JACK_CLIENT_NAME="Vital"
#         JUCE_ALSA_MIDI_INPUT_NAME="Vital"
#         JUCE_ALSA_MIDI_OUTPUT_NAME="Vital"
#         JUCE_USE_XRANDR=0
#         LINUX=1
#         JUCE_LINUX=1
#     )
# endif()

# set_property(TARGET vital_tests PROPERTY CXX_STANDARD 14)

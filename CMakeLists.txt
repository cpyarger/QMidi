cmake_minimum_required(VERSION 3.10)
# Change obs-plugintemplate to your plugin's name in a machine-readable format
# (e.g.: obs-myawesomeplugin) and set
project(QMidi VERSION 0.2.4)

# Replace `Your Name Here` with the name (yours or your organization's) you want
# to see as the author of the plugin (in the plugin's metadata itself and in the installers)
set(PLUGIN_AUTHOR "waddlesplash")

# Replace `com.example.obs-plugin-template` with a unique Bundle ID for macOS releases
# (used both in the installer and when submitting the installer for notarization)
set(MACOS_BUNDLEID "com.example.QMidi")

# Replace `me@contoso.com` with the maintainer email address you want to put in Linux packages
set(LINUX_MAINTAINER_EMAIL "cpyarger@gmail.com")

if(WIN32)
	set(MODULE_DESCRIPTION "QMIDI module")
endif()

set(CMAKE_PREFIX_PATH "${QTDIR}")
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_definitions(-DASIO_STANDALONE)


set(qmidi_SOURCES
	src/QMidiOut.cpp
	src/QMidiFile.cpp 
	src/QMidiIn.cpp)
set (qmidi_HEADERS
	src/QMidiOut.h 
	src/QMidiFile.h 
	src/QMidiIn.h)


if(WIN32)
	list(APPEND qmidi_SOURCES
		src/OS/QMidi_Win32.cpp)
endif()
if(LINUX)
	list(APPEND qmidi_SOURCES
		src/OS/QMidi_ALSA.cpp)
	list(APPEND qmidi_HEADERS
		src/OS/QMidi_ALSA.h)
endif()
if(HAIKU)
	list(APPEND qmidi_SOURCES
		src/OS/QMidi_Haiku.cpp)
	list(APPEND qmidi_HEADERS
		src/OS/QMidi_Haiku.h)
endif()

if(APPLE)
	list(APPEND qmidi_SOURCES
		src/OS/QMidi_CoreMidi.cpp)

endif()


add_library(QMidi MODULE
        ${qmidi_SOURCES}
        ${qmidi_HEADERS})


if(WIN32)
target_link_libraries(QMidi ${_public} winmm)
endif()
if(LINUX)
	find_package(ALSA)
		if(ALSA_FOUND)
			target_link_libraries(QMidi ${_public} ${ALSA_LIBRARIES})
	    	endif()
endif()
if (HAIKU)
endif()
if (APPLE)
	find_library(COREMIDI_LIBRARY CoreMIDI)
	find_library(COREAUDIO_LIBRARY CoreAudio)
	find_library(COREFOUNDATION_LIBRARY CoreFoundation)
	target_link_libraries(QMidi
		${_public}
	        ${COREFOUNDATION_LIBRARY}
	        ${COREAUDIO_LIBRARY}
	        ${COREMIDI_LIBRARY})
endif()
set_target_properties(QMidi PROPERTIES FOLDER "Control Plugins/Externals")
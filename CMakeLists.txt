cmake_minimum_required(VERSION 3.10)
# Change Qplugintemplate to your plugin's name in a machine-readable format
# (e.g.: Qmyawesomeplugin) and set
project(QMidi VERSION 0.2.4)

# Replace `Your Name Here` with the name (yours or your organization's) you want
# to see as the author of the plugin (in the plugin's metadata itself and in the installers)
set(PLUGIN_AUTHOR "cpyarger")

# Replace `com.example.Qplugin-template` with a unique Bundle ID for macOS releases
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
find_package(Qt5 REQUIRED COMPONENTS Core Widgets)


include_directories(
        SYSTEM
	{Qt5Core_INCLUDES}
	${Qt5Widgets_INCLUDES}
)
if(WIN32)
list(APPEND Qmidi_SOURCES
src/OS/QMidi_Win32.cpp)
elseif(LINUX)
list(APPEND Qmidi_SOURCES
src/OS/QMidi_ALSA.cpp)
list(APPEND Qmidi_HEADERS
src/OS/QMidi_ALSA.h)

elseif(APPLE)
list(APPEND Qmidi_SOURCES
src/OS/QMidi_CoreMidi.cpp)

endif()
list(APPEND Qmidi_SOURCES

${midi_ui_headers}
	src/QMidiIn.cpp
	src/QMidiOut.cpp
)

list(APPEND Qmidi_HEADERS
	src/QMidiIn.h
	src/QMidiOut.h
	src/QMidiFile.h
)


add_library(QMIDI MODULE
	${Qmidi_UI_HEADERS}
        ${Qmidi_SOURCES}
        ${Qmidi_HEADERS})

target_link_libraries(QMIDI	
			Qt5::Core
			Qt5::Widgets
				)
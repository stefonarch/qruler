cmake_minimum_required(VERSION 3.6)
project(qruler VERSION 0.1.0 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(UPDATE_TRANSLATIONS "Update source translations" OFF)

find_package(Qt5 COMPONENTS Widgets LinguistTools REQUIRED)
include(GNUInstallDirs REQUIRED)

set(APPSTREAM_ID "qruler")
set(DESKTOP_FILE_IN
    resources/${APPSTREAM_ID}.desktop.in
)

#set(TS_FILES resources/translations/qruler_it_IT.ts)

set(SRC_FILES
    src/dialogprefs.hpp
    src/dialogprefs.cpp
    src/dialogprefs.ui
    src/qruler.hpp
    src/qruler.cpp
    src/settings.hpp
    src/settings.cpp
    src/application.hpp
    src/application.cpp
    resources/resources.qrc
)

# Translations
#===============================================================================
set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_CURRENT_SOURCE_DIR}/cmake")
set(LXQT_CMAKE_MODULES_DIR "${CMAKE_MODULE_PATH}/LXQTBuildTools")

include(LXQTBuildTools/LXQtTranslateDesktop)
include(LXQTBuildTools/LXQtTranslateTs)

lxqt_translate_ts(QM_FILES
    UPDATE_TRANSLATIONS ${UPDATE_TRANSLATIONS}
    SOURCES ${SOURCE_FILES}
    TRANSLATION_DIR "resources/translations"
    INSTALL_DIR ${CMAKE_INSTALL_DATADIR}/${CMAKE_PROJECT_NAME}/translations
)

lxqt_translate_desktop(DESKTOP_FILE
    SOURCES ${DESKTOP_FILE_IN}
    TRANSLATION_DIR "resources/translations"
    USE_YAML
)
qt5_wrap_ui(UI_SOURCE ${UI_FILES})
set_property(SOURCE ${UI_SOURCE} PROPERTY SKIP_AUTOMOC ON)
#qt5_create_translation(QM_FILES ${CMAKE_SOURCE_DIR/resources} ${TS_FILES})

add_executable(${CMAKE_PROJECT_NAME}
    ${SRC_FILES}
    ${QM_FILES}
    ${DESKTOP_FILE}
)

target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE
    Qt5::Widgets
)
if (UNIX AND NOT APPLE)
    string(TOLOWER ${CMAKE_PROJECT_NAME} APP_NAME)
    set(APPSTREAM_BASENAME "org.azdrums.${APP_NAME}")
    set_target_properties(${CMAKE_PROJECT_NAME} PROPERTIES
        OUTPUT_NAME "${APP_NAME}"
    )
    include (GNUInstallDirs)
    install (TARGETS ${CMAKE_PROJECT_NAME}
        DESTINATION ${CMAKE_INSTALL_BINDIR})
    install (FILES resources/icons/${APP_NAME}.svg
        DESTINATION ${CMAKE_INSTALL_DATADIR}/icons/hicolor/scalable/apps)
    install (FILES resources/linux/${APPSTREAM_BASENAME}.appdata.xml
        DESTINATION ${CMAKE_INSTALL_DATADIR}/metainfo)
    install(FILES ${DESKTOP_FILE}
        DESTINATION ${CMAKE_INSTALL_DATADIR}/applications)
    install (FILES ${QM_FILES}       DESTINATION ${CMAKE_INSTALL_PREFIX}/share/${APP_NAME}/translations)
endif()

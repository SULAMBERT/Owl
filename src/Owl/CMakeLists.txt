# Owl - www.owlclient.com
# Copyright (c) 2012-2017, Adalid Claure <aclaure@gmail.com>

PROJECT(Owl)

# we need this to be able to include headers produced by uic in our code
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR})
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

OPTION(SUPRESS_CONSOLE_WINDOW "Supress TerminalWindow console" ON)

SET (SOURCE_FILES
    AboutDlg.cpp
    AspectRatioPixmapLabel.cpp
    BoardTreeView.cpp
    BoardsModel.cpp
    BoardUpdateWorker.cpp
    ClickableLabel.cpp
    ConfiguringBoardDlg.cpp
    EditBoardDlg.cpp
    ErrorReportDlg.cpp
    FocusEventLineEdit.cpp
    main.cpp
    MainWindow.cpp
    NewThreadDlg.cpp
    PostTextEditor.cpp
    PreferencesDlg.cpp
    OwlApplication.cpp
    PostListWidget.cpp
    QuickAddDlg.cpp
    ThreadListWidget.cpp
)

SET (MOC_HEADERS
    AboutDlg.h
    AspectRatioPixmapLabel.h
    BoardsModel.h
    BoardTreeView.h
    BoardUpdateWorker.h
    ClickableLabel.h
    ConfiguringBoardDlg.h
    EditBoardDlg.h
    ErrorReportDlg.h
    FocusEventLineEdit.h
    MainWindow.h
    NewThreadDlg.h
    OwlApplication.h
    PostListWidget.h
    PostTextEditor.h
    PreferencesDlg.h
    QuickAddDlg.h
    ThreadListWidget.h
)

SET (UI_SOURCES
    AboutDlg.ui
    ConfiguringBoardDlg.ui
    EditBoardDlg.ui
    ErrorReportDlg.ui
    NewThreadDlg.ui
    PreferencesDlg.ui
    QuickAddDlg.ui
    MainWindow.ui
)

SET (HEADER_FILES
    Core.h
    ${MOC_HEADERS}
)

set(QRC_SOURCE_FILES 
    res/Owl.qrc
)

file(GLOB_RECURSE QML_FILES res/qml/*.qml res/qml/*.js)

add_subdirectory(Data)

IF (APPLE)
    SET(RESOURCE_FILES "Resources/Owl.icns")
ENDIF()

if (WIN32)
    SET(CMAKE_EXE_LINKER_FLAGS "/NODEFAULTLIB:library")
    SET(RESOURCE_FILES "res/Owl.rc")
endif()

QT5_ADD_RESOURCES(RRC_SOURCE_FILES ${QRC_SOURCE_FILES})
QT5_WRAP_UI(UI_HEADERS ${UI_SOURCES})
QT5_WRAP_CPP(MOC_SOURCE_FILES ${MOC_HEADERS})

MESSAGE(STATUS "SUPRESS_CONSOLE_WINDOW: " ${SUPRESS_CONSOLE_WINDOW})
IF(${SUPRESS_CONSOLE_WINDOW})
	SET(GUI_TYPE WIN32)
ENDIF(${SUPRESS_CONSOLE_WINDOW})

SET(DEPENDENCY_LIST 
    Data
    Parsers
    Utils
)

SET_SOURCE_FILES_PROPERTIES(
    ${RESOURCE_FILES}
    PROPERTIES
    MACOSX_PACKAGE_LOCATION Resources
)

ADD_EXECUTABLE(Owl ${GUI_TYPE} MACOSX_BUNDLE
    ${SOURCE_FILES}
    ${MOC_SOURCE_FILES}
    ${RRC_SOURCE_FILES}
    ${HEADER_FILES}
    ${RESOURCE_FILES}
    ${UI_HEADERS}
    ${QML_FILES}
)
	
TARGET_LINK_LIBRARIES(Owl 
    ${DEPENDENCY_LIST}
    ${QT_LIBRARIES}
    ${LOG4QT_LIBRARIES}
    ${HTMLTIDY_LIBRARIES}
    ${HUNSPELL_LIBRARIES}
)

SET(QT_MODULE_LIST
    Concurrent
    Core
    Network
    Quick
    QuickWidgets
    Sql
    UiTools
    WebEngine
    WebEngineWidgets
    Widgets
    Xml
    XmlPatterns
)

if (APPLE)
    list(APPEND QT_MODULE_LIST MacExtras)
endif (APPLE)

QT5_USE_MODULES(Owl 
    ${QT_MODULE_LIST}
)

INSTALL(
    TARGETS Owl
    COMPONENT Owl
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    BUNDLE DESTINATION .
)

set(LUA_PARSERS
    parser-vbulletin3x.lua
)

# the folder where Owl's default config files exist
set(OWL_CONFIG_FOLDER "${CMAKE_CURRENT_SOURCE_DIR}/config")

# folder in which the dictionaries live
set(OWL_DICT_FOLDER "${CMAKE_CURRENT_SOURCE_DIR}/../../dictionaries")

if (APPLE)
    SET(MACOSX_BUNDLE_BUNDLE_NAME Owl)
    SET(MACOSX_BUNDLE_ICON_FILE Owl.icns)
    SET(MACOSX_BUNDLE_GUI_IDENTIFIER "com.owlclient.full")
    SET(MACOSX_BUNDLE_BUNDLE_VERSION "100")
    SET(MACOSX_BUNDLE_LONG_VERSION_STRING "Owl Version ${OWL_VERSION}")
    SET(MACOSX_BUNDLE_SHORT_VERSION_STRING "${OWL_VERSION}")
    SET(MACOSX_BUNDLE_COPYRIGHT "Copyright (c) 2012-2017, Adalid Claure")

    #SET_TARGET_PROPERTIES(Owl PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "Mac Developer")
    #SET_TARGET_PROPERTIES(Owl PROPERTIES XCODE_ATTRIBUTE_OTHER_CODE_SIGN_FLAGS "--deep")

    # the escaped \$ in \${CONFIGURATION} is an Xcode variable
    # see: http://stackoverflow.com/questions/13420117/how-to-get-a-path-of-the-app-bundle-in-a-cmake-script-to-supply-it-to-the-fixup
    if("${CMAKE_GENERATOR}" MATCHES "Xcode")
            set(DEST_BIN_PATH ${CMAKE_BINARY_DIR}/Owl/\${CONFIGURATION}/Owl.app/Contents/Resources)
    elseif (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
            set(DEST_BIN_PATH ${CMAKE_BINARY_DIR}/Owl/Owl.app/Contents/Resources)
    endif()

    # NOTE: this may need to change for non-Xcode compilers
    set(BUNDLE_PATH ${CMAKE_BINARY_DIR}/Owl/Release/Owl.app)

    # copy the default user settings file (owl.ini)
    ADD_CUSTOM_COMMAND(TARGET Owl POST_BUILD
        COMMAND "cp" ARGS "${OWL_CONFIG_FOLDER}/macOS/owl.ini" "${DEST_BIN_PATH}"
        COMMENT "Copying owl.ini")

    # copy the included parsers
    if (LUAPARSERS_FOLDER)
        foreach(PARSER ${LUA_PARSERS})
            ADD_CUSTOM_COMMAND(TARGET Owl POST_BUILD
                COMMAND ${CMAKE_COMMAND} -E copy_if_different
                        "${LUAPARSERS_FOLDER}/${PARSER}"
                        "${DEST_BIN_PATH}/parsers/${PARSER}"
                COMMENT "Copying parser ${LUAPARSERS_FOLDER}/${PARSER}")
        endforeach(PARSER)
    endif(LUAPARSERS_FOLDER)

    # copy the included dictionaries
    ADD_CUSTOM_COMMAND(TARGET Owl POST_BUILD
        COMMAND "cp" ARGS -R "${OWL_DICT_FOLDER}" "${DEST_BIN_PATH}"
        COMMENT "Copying dictionaries folder")

elseif (WIN32)
    # MESSAGE("OWL_CONFIG_FOLDER: ${OWL_CONFIG_FOLDER}")
    # MESSAGE("OWL_PARSERS_FOLDER: ${OWL_PARSERS_FOLDER}")
    # MESSAGE("DEST_BIN_PATH: ${CMAKE_BINARY_DIR}")

    FILE(TO_NATIVE_PATH "${OWL_CONFIG_FOLDER}/Windows/owl.ini" OWL_INI_TO_COPY)
    ADD_CUSTOM_COMMAND(TARGET Owl POST_BUILD
        COMMAND "xcopy" ARGS /Y /D "${OWL_INI_TO_COPY}" "$(OutDir)"
        COMMENT "Copying owl.ini")
elseif (LINUX)
    # nothing yet for Linux		
endif()

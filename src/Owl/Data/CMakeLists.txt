# Owl - www.owlclient.com
# Copyright (c) 2012-2017, Adalid Claure <aclaure@gmail.com>

project(Data)

# we need this to be able to include headers produced by uic in our code
include_directories(${CMAKE_CURRENT_BINARY_DIR})

set (SOURCE_FILES
    Board.cpp
    BoardManager.cpp
)

set (MOC_HEADERS
    Board.h
    BoardManager.h
)

set (HEADER_FILES
    ${MOC_HEADERS}
)

qt5_wrap_cpp(MOC_SOURCE_FILES ${MOC_HEADERS})

set(DEPENDENCY_LIST
    log4qt
    Parsers
    Utils
)

add_library(Data STATIC
    ${SOURCE_FILES}
    ${HEADER_FILES}
    ${MOC_SOURCE_FILES}
)

target_link_libraries(Data
    Qt5::Concurrent
    Qt5::Core
    Qt5::Gui
    Qt5::Sql
    Qt5::Xml
)

Max OSX Build Instructions and Notes
====================================

Requirements
----
* Qt 5.4.2 (http://qt-project.org/downloads)
* Lua 5.2.1 (http://luabinaries.sourceforge.net/download.html)
* boost 1.6.0 (http://www.boost.org)

CMake Notes
----
* **BOOST_INCLUDE_DIR** - Set to instalation folder of Boost<br/> 
*Example: /Users/myuser/Downloads/boost_1_58_0*

* **LUA_FOLDER** - Set to the base folder of Lua, expecting {lua}/include and {lua}/lua52.a<br/>
*Note: Make sure this doesn't point to the system's install of Lua as that may not be the correct*.<br/>
*Example: /Users/addy/Downloads/lua-5.2.1_MacOS107_lib*

* **LUA_LIBRARY_RELEASE**: This had to be set manually to the *.a file in the Lua folder in order for OwlConsole to run correctly in QtCreator

* **QT5_FOLDER** - Set to the root of the compiler tool's CMake files<br/>
*Example: /Users/aclaure/Qt5.4.2/5.4/clang_64/*
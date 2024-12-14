---
author: Amirreza
layout: post
title: Cmake guide and tricks
categories: [software development]
tags: [Build systems, qt, cmake]
---

# A recipe for a basic working `CmakeLists.txt`

## General Tips:
- First of all, be careful about the case sensitivity of cmake. **All arguments-the things coming between paranthesis- are case sensitive.**

## Qt Basic code steps:
1. `project(NAME LANGUAGES CXX)`: you need to specify a name for the project and the language of the source files it includes. cxx is an older name for cpp.
2. `find_package(Qt REQUIRED COMPONENTS ...)`: the packages you require the running machine to have installed.
3. `qt_add_executable(TARGETNAME .cpp .h)` create target name. Targets are a bundle of resources, libraries that the project requires you to have.
4. `set_target_properties(TARGETNAME PROPERTIES
    WIN32_EXECUTABLE TRUE
)` I don't exactly know what this should do!
5. `target_link_libraries(<target>...<item>)`
6. `install(<target>... 
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)`
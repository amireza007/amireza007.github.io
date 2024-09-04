---
authors: amireza007 
layout: post
title: Install VTK and integrate it in QT
date: '2024-08-19 14:31 +0330'
categories: [Software Development]
tags: [QT, VTK]
--- 

## Installation process

- follow the tutorial explained in [this page](https://gitlab.kitware.com/vtk/vtk/-/blob/master/Documentation/docs/build_instructions/build.md) clone this project from the [this gitlab](https://gitlab.kitware.com/vtk/vtk)
- use `cmake` instead of `ccmake`
- change config in cmake-gui to `Release`
- after `configuring` for the first time, remember to change the `use qt` (or smth similar) form `default` to `YES`
- after generating the build files, you need to build the project using the `x64 native tools` cmd
- After the build is completed, you need to install it using the build system you chose (for integrating and using qtcreator, use `ninja`)
- re-run the `x64 native tools` cmd as Admin
- cd to the build folder, then do a `ninja install`
- At last, create a variable in the Environment setting, called `VTK_DIR` and point it to the installed location (the defaule in cmake-gui is `C:\Program Files (x86)\VTK\lib\cmake\vtk-9.3`)
- it's done!
  
---

## Integration process
- In order to actually be able to use the vtk in a project, you need to set up the `CMakeLists.txt`. I can be done inside the QtCreator (under the `projects` setdxtings) or outside it from the cmake-gui (the same process.)
- In either cases, you need to make the vtk be known to the cmake. You can call the tool by defining a path entry, called `VTK_DIR` and assign it the value of `VTK_DIR` variable, defined earlier. 
- Then run the cmake. And Enjoy using VTK on Qt Projects!
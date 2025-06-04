 ---
authors: amireza007
layout: post
title: Custom Widgets in Qt
date: '2024-09-03 17:45 +0330'
categories: [Software Development]
tags: [Qt, Gui, UI Design, Qt Designer]
---

In this, I'll be writing about the things I've learned during implementing the UI of MDSAGV with the help of Custom widgets.

### Useful Links:
- [https://doc.qt.io/qt-6/designer-creating-custom-widgets.html](https://doc.qt.io/qt-6/designer-creating-custom-widgets.html)
- For distinguishing between Qt plugin and Widget: [this](https://doc.qt.io/qt-5/plugins-howto.html)
- This one is useful for general Qt, providing all **Macros** used in custom classes. [This](https://doc.qt.io/qt-6/functions.html)
- an [example](https://doc.qt.io/qt-6/qtdesigner-customwidgetplugin-example.html) of custom plugin
- [qt_add_plugin](https://doc.qt.io/qt-6/qt-add-plugin.html)
- Did you know you can start `Qt-Creator` from **Command Line**. You can use this to set custom plugin paths for **qt designer** plugin in **qt creator**.
 
    ```
    \path\to\qt\installation_Directory\Tools\QtCreator\bin\qtcreator.exe -h
    ```
- For adding the ui file to a class and `.h` file in the project directory, **You kneed to promote the plugin/custom widget in `QT-Designer` to that class**

---

### Useful Videos:
- [Custom Widget in Qt, Iranian Guy](https://www.youtube.com/watch?v=2AiWla1cc6Q)
- [A more Practical Video, fat Guy](https://www.youtube.com/watch?v=RyJqcw0RXxk)

### Useful Tricks
- Use `<alt><shift>r` to view your widget's `[preview]`, kinda works like `Qml Live`.
- for using `_s` string formats, see this [https://doc.qt.io/qt-6/qt-literals-stringliterals.html](https://doc.qt.io/qt-6/qt-literals-stringliterals.html)

### Recipe for making custom widgets:
1. 
2. 
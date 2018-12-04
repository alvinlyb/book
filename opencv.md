### mac10.12 opencv for Qt5.9 
在mac 10.12上的pro配置
```c++
QT += core
QT -= gui

CONFIG += c++11

TARGET = opencv
CONFIG += console
CONFIG -= app_bundle

TEMPLATE = app

SOURCES += main.cpp

DEFINES += QT_DEPRECATED_WARNINGS

#下面部分自己添加
#用于识别编译环境为mac10.12
QMAKE_MACOSX_DEPLOYMENT_TARGET=10.12
QMAKE_MAC_SDK=macosx10.12

#在编译时出现error: symbol(s) not found for architecture x86_64错误时添加以下三行
QT_CONFIG -=no-pkg-config
CONFIG += link_pkgconfig
PKGCONFIG += opencv
#包含的opencv库和头文件
include($$PWD/OpencvConfig.pri)
```
OpencvConfig.pri配置文件,此文件单独导入到*.pro中，也可以直接写入*.pro文件中。
```c++
INCLUDEPATH += /usr/local/include
#INCLUDEPATH += usr/local/Cellar/opencv/3.3.1_1/include/opencv
#INCLUDEPATH += /usr/local/Cellar/opencv/3.3.1_1/include


LIBS += /usr/local/lib/libopencv_core.dylib
LIBS += /usr/local/lib/libopencv_highgui.dylib
LIBS += /usr/local/lib/libopencv_imgproc.dylib

#LIBS += /usr/local/Cellar/opencv/3.3.1_1/lib/libopencv_core.3.3.dylib
#LIBS += /usr/local/Cellar/opencv/3.3.1_1/lib/libopencv_highgui.3.3.dylib
#LIBS += /usr/local/Cellar/opencv/3.3.1_1/lib/libopencv_imgproc.3.3.dylib
```

### window opencv for Qt
在window上的Qt，pro配置文件配置opencv.
```c++
#下面加入了两个宏，否则编译连接出镜
DEFINES+=DCURL_STATICLIB BUILDING_LIBCURL

LIBS += C:/cxxlib/libcurl_vc14_x64_debug/lib/libcurl_a_debug.lib
INCLUDEPATH += C:/cxxlib/libcurl_vc14_x64_debug/include
```


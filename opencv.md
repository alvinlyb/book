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
## Opencv

## 函数

### cv

说明：函数的参数中使用source为源图像，target为目标参数

```c++
Mat source=imread("source.jpg",CV_LOAD_IMAGE_COLOR);

Mat target;

```

#### cv::VideoCapture

```
Mat video = VideoCapture(int index);
```

1.  index 为整型输入端的序号一般0为摄像头

   ```c++
   Mat frame;
   VideoCapture video(0);
   if (!video.isOpened())	//如果video视频端口没有打开返回退出
   {
   	return -1;
   }
   while (video.read(frame))	//将video信号读到ovtrnd到frame的Mat类型中
   {
   	imshow("video", frame);
   	if (waitKey(1000 / fps) >= 0)
   		break;
   
   }
   ```

   以上为一段代码的使用方法。并带相关函数。

#### cv::split

```c++
Mat source=imread("source.jpg",CV_LOAD_IMAGE_COLOR);
Mat target[3];
cv::split(source,target)
```

分离原图像source为三通道的图像。分离为BGB分别为target[0],target[1],target[2]。

#### cv::merge

```
Mat source[3];
Mat output;
cv::merge(source,3,output);
```

把source多个通道的图片合并输出到output中。形成新的图片。

#### cv::imshow

```
cv::imshow("windowtitle",source);
```

显示source图像。

1. "windowtitle"：显示窗口的标题。

#### cv::imread

```c++
cv::Mat file1=cv::imread("qq.jpg",CV_LOAD_IMAGE_COLOR);
```

加载图片为Mat类型。

1. 加载的"qq.jpg"文件路径。
2. 加载的类型CV_LOAD_IMAGE_COLOR,等参数宏。 

#### cv::waitKey 

```
cv::waitKey(int delay=0);
```

等代键盘输入按键。

1.delay参数为int型，默认为0.

### Mat

#### Mat::zeros

```
Mat source=imread("source.jpg",CV_LOAD_IMAGE_COLOR);
Mat target[3];
cv::split(source,target)
target[2]=Mat::zeros(target[2].size(),CV_8UC1);	//对应像素填充为0
```

zeros把对应的一个通道或多个通道的像素填充为0值。相当于清除了该通道的所有信息。

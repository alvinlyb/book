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


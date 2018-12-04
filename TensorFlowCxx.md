## TFCXX编辑与配置

### 一、所需软件工具

1.CMake version 3.5以上

2.git (http://git-scm.com)

3.swig (http://www.swig.org/download.html)解压即可

4.Visual Studio 2015

5.UCDA9.0 (https://developer.nvidia.com/cuda-90-download-archive)

> 注意:在2018.08.24是编译的tensorFlow版本时使用ucda9.0

6.CUDNN一定要对应上面的UCDA9.0版(https://developer.nvidia.com/cudnn)下载后解压合并到安装的UCDA/v9.0目录下。

6.Python 3.5 64-bit (选择添加安装lib文件)

7.NumPy 1.11.0 以上

>  注意事项：CMake 与 git安装时需要将其加入到环境变量中，否则后续编译过程中会出现找不到相关程序的错误

二、以上所需工具全部安装全后，在guit拉下TensorFlow的源码：

https://github.com/tensorflow/tensorflow.git

```cmake
git clone https://github.com/tensorflow/tensorflow
cd tensorflow
#创建这个build目录
mkdir build
#使用cmake创建cmake文件建意使用cmake-gui
cmake ../tensorflow/contrib/cmake
```

三、注意在第一步时一定要把所有配置好才能使用cmake生成vs2015 x64的lsn
Qt Installler Framework
===
## 简介
Qt Installler Framework是Qt中的一个制作安装包的工具
## 使用方法
#### 一、首先配置config\config.xml文件和
#### 二、把需要打包的dll和exe等文件合部拷贝到ackages\org.qtproject.ifw.example\data下。
#### 三、配置packages\org.qtproject.ifw.example\meta下的package.xml文件
#### 四、如果需要license提示。需要在packages\org.qtproject.ifw.example\meta下license.txt权板信息文件.
#### 五、最后使用命令行生成exe安装包。如下：
```bash
cd e:\temp\startmenu
binarycreator.exe -c config\config.xml -p packages my_installer.exe -v
```

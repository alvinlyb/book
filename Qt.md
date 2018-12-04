```c++
    /*
     * 分析：程序崩溃，因为label被close时，delete &label;但label对象是在栈上分配的内存空间，
     * delete栈上的地址会出错。有些朋友理解为label被delete两次而错误，

     * 可以测试QLabel label(“Hello Qt!”); label.show();delete &label;第一次delete就会出错。
     */
    //QLabel *label=new QLabel("hello");
    QLabel label("hello");
    label.show();
    label.setAttribute(Qt::WA_DeleteOnClose);
```

#### 使用vs2015 编译 qt for maya2015出现的link 2011错误

需要使用maya2015\bin\moc.exe生成对应的moc_xxx.cpp并添加到项目中。

```bash
moc "helixQtCmd.h" -o "moc_helixQtCmd.cpp"
```

### 特化偏平化的类型对比

```c++
template<typename T>
struct is_type
{
    //重载操作符
    operator bool()
    {
        return false;
    }
 //操行的操作
    void Do(){
    	std::cout<<"hello";
    }
};
template<>
struct is_type<QUrl>
{
//重载操作符
	operator bool()
    {
    	return true;
    }
    //操行的操作
    void Do(){
    	std::cout<<"hello";
    }
};
template<class T>
void function(T str)
{
    if(is_type<T>()){
        qDebug()<<"QUrl";
    }else{
        qDebug()<<"默认假";
    }
    //操作
    is_type<T> t;
    t.Do();
}
```



视图与模

QTableView

QTableWidget

QListView

QListWidget

### Qt的四个图片处理类

QImage、QPixmap、QBitmap、QPicture

## Qss

#### QLabel

```css
QLabel{
    /* 相当于 font: bold 50px "Snell Roundhand"; */
    font-size: 50px;
    font-weight: bold;
    font-family: "Snell Roundhand";
    /* 文本的颜色 */
    color: white;
    /* 相当于 background: lightgray url(:/resources/horizontal-add-line.png); */
    background-color: lightgray;
    background-image: url(:/resources/horizontal-add-line.png);
    /* 还能使用渐变 */
    background-color: qlineargradient(x1: 0, y1: 0, x2: 0, y2: 1,
                      stop: 0 #FFFFFF, stop: 1 #BB000000);
    /* 相当于 border: 5px solid gray; */	
    border-width: 5px;
    border-color: gray;
    border-style: solid;
    /* 边框圆角 */
    border-radius: 10px;
    padding: 5px;
    margin: 10px
}
```




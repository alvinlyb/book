# opengl
## opengl学习笔
### 函数部分
- 顶点数组对象：Vertex Array Object,VAO
- 顶点缓冲对象：Vertex Buffer Object,VBO
- 索引缓冲对象：Element Buffer Object,EBO或IBO

#### glGenVertexArrays
创建点顶数组对象
```c++
GLuint VAO;
glGenVertexArrays(1, &VAO);
```
#### glGenerateMipmap

```c++
glGenerateMipmap(GL_TEXTURE_2D);
```

创建纹理贴图

####  glBindVertexArray

绑定VAO对象
```c++
glBindVertexArray(VAO); //绑定VAO
```
对应的使用:
```
 glBindVertexArray(0); //解除绑定
```

#### glGenBuffers
生成顶点缓冲VBO,顶点缓冲对象的缓冲类型是GL_ARRAY_BUFFER

#### glIsBuffer

```
GLboolean glIsBuffer(GLuint buffer);
```

判断是否是缓冲区对像

#### glBindBuffer
OpenGL有很多缓冲对象类型，顶点缓冲对象的缓冲类型是GL_ARRAY_BUFFER
```c++
glBindBuffer(GL_ARRAY_BUFFER, VBO); //把新创建的VBO绑定到GL_ARRAY_BUFFER
```
绑定顶点缓冲到GL_ARRAY_BUFFER

#### glBufferData
把顶点数据复制到顶点缓冲内存中。
```c++
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```
参数：
1. 目标缓冲的类型:GL_ARRAY_BUFFER
2. 指定传输数据的大小(以字节为单位), 用一个简单的sizeof计算出顶点数据大小就行。
3. 发送的实际数据。
4. 指定了我们希望显卡如何管理给定的数据。它有三种形式：
* GL_STATIC_DRAW ：数据不会或几乎不会改变。
* GL_DYNAMIC_DRAW：数据会被改变很多。
* GL_STREAM_DRAW ：数据每次绘制时都会改变。

#### glCreateShader
创建shader着色器
```c++
GLuint vertexShader;
vertexShader=glCreateShader(GL_VERTEX_SHADER); //创建一个顶点着色器
```
#### glBindVertexArray
```c++
glBindVertexArray(VAO);
```
#### glDrawArrays
绘制图元.
```c++
glDrawArrays(GL_TRIANGLES,0,3);
```
参数:
1. 第一个参数绘制的图元类型。
2. 第二个参数指定顶点数组的起始索引。
3. 最后一个参数指定我们打算绘制多少个顶点.

#### glDrawElements
当使用EBO索引缓冲对象时，使用些函数替代glDrawArrays来指明我们从索引缓冲绘制图元。
```c++
glDrawElements(GL_TRIANGLES,6,GL_UNSIGNED_INT,0);
```
参数:
1. 第一个参数绘制的图元类型。
2. 第二个参数指定绘制顶点的个数，也就是一共需要绘制多个顶点。
3. 第三个参数是索引的类型，这里是GL_UNSIGNED_INT。
4. 最后一个参数里我们可以指定EBO中的偏移量，（或者传递一个索引数组，但是这是当你不在使用索引缓冲对象的时候），但是我们会在这里填写0。

#### glBindVertexArray

#### glGetShaderiv
检查GLSL语言是否编译成功
```c++
GLuint shader;
glShaderSource(shader,1,&vShaderCode,NULL); //源化vertex
glCompileShader(shader);    //编译shader
int success;
char infoLog[512];
glGetShaderiv(shader, GL_COMPILE_STATUS, &success);
//如果编译失败
if (!success)
{
    glGetShaderInfoLog(shader, 1024, NULL, infoLog);
    std::cout << "ERROR::SHADER_COMPILATION_ERROR of type: " << type << "\n" << infoLog << "\n -- --------------------------------------------------- -- " << std::endl;
}
```
#### glGetShaderInfoLog
获取shader错误消息
```c++
glGetShaderInfoLog(vertex, 1024, NULL, infoLog);
```
#### glCreateProgram;
```c++
GLuint ID = glCreateProgram();
```
创建一个运行在显卡的着色器小程序。

#### glAttachShader
```c++
glAttachShader(ID, vertex);
```
把glsl写的着色器附加到着色器小程序上

#### glLinkProgram
```c++
glLinkProgram(ID);  //链接程序
```
把附加的着色器与小程序链接.

#### glGetProgramiv
检查着色器小程序是否编译连接成功
```c++
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
```
#### glGetProgramInfoLog
与glGetShaderInfoLog一样，但这个是获得小程序的错误消息.
```
glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
```
#### glUseProgram
激活运行着色器小程序
```
glUseProgram(shaderProgram);
```

#### glDeleteShader
删除着色器对象
```c++
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```

#### glVertexAttribPointer
顶点属性解析
```c++
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(GLfloat), (GLvoid*)0);
glEnableVertexAttribArray(0);
```
1. 第一个参数指定我们要配置的顶点属性。还记得我们在顶点着色器中使用layout(location = 0)定义了position顶点属性的位置值(Location)吗？它可以把顶点属性的位置值设置为0。因为我们希望把数据传递到这一个顶点属性中，所以这里我们传入0。

2. 第二个参数指定顶点属性的大小。顶点属性是一个vec3，它由3个值组成，所以大小是3。

3. 第三个参数指定数据的类型，这里是GL_FLOAT(GLSL中vec*都是由浮点数值组成的)。

4. 下个参数定义我们是否希望数据被标准化(Normalize)。如果我们设置为GL_TRUE，所有数据都会被映射到0（对于有符号型signed数据是-1）到1之间。我们把它设置为GL_FALSE。

5. 第五个参数叫做步长(Stride)，它告诉我们在连续的顶点属性组之间的间隔。由于下个组位置数据在3个GLfloat之后，我们把步长设置为3 * sizeof(GLfloat)。要注意的是由于我们知道这个数组是紧密排列的（在两个顶点属性之间没有空隙）我们也可以设置为0来让OpenGL决定具体步长是多少（只有当数值是紧密排列时才可用）。一旦我们有更多的顶点属性，我们就必须更小心地定义每个顶点属性之间的间隔，我们在后面会看到更多的例子(译注: 这个参数的意思简单说就是从这个属性第二次出现的地方到整个数组0位置之间有多少字节)。
最后一个参数的类型是GLvoid*，所以需要我们进行这个奇怪的强制类型转换。它表示位置数据在缓冲中起始位置的偏移量(Offset)。由于位置数据在数组的开头，所以这里是0。我们会在后面详细解释这个参数

#### glEnableVertexAttribArray
```c++
glEnableVertexAttribArray(0);
```
启用顶点属性，顶点属性默认是禁用的，所以设置顶点属性后要打开。

#### glPolygonMode
以线框模式绘制模型
```c++
glPolygonMode(GL_FRONT_AND_BACK, GL_LINE)
```
参数：

1. 第一个参数表示我们打算将其应用到所有的三角形的正面和背面。
2. 第二个参数告诉我们用线来绘制。

#### glEnable

```c++
glEnable(GL_DEPTH_TEST);
```

打开opengGL的各种测试。

### 宏
#### GL_FRONT_AND_BACK
应用正面和背面

#### GL_LINE
用线来绘制

#### GL_VERTEX_SHADER
创建的是点顶shader着色器

#### GL_FRAGMENT_SHADER
创建的是片段着色器

#### GLFW_KEY_W
```c++
bool keyPressedStatus[1024]; // 按键情况记录
if (keyPressedStatus[GLFW_KEY_W])
		camera.handleKeyPress(FORWARD, deltaTime);
```
键盘w键。

#### GLFW_KEY_S

键盘s键。

#### GLFW_KEY_A

键盘a键。

#### GLFW_KEY_D

键盘d键。

#### FORWARD
向前
#### BACKWARD
后
#### LEFT
左
#### RIGHT
右

## glfw

#### glfwInit

用于初始化opengl。

#### glfwWindowHint

用于设置opengl版本。

```
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
```



#### glfwMakeContextCurrent

```
glfwMakeContextCurrent(window);
```

设置上下文。

#### glfwSetFramebufferSizeCallback

设置帧缓冲大小回调函数。

#### glfwSetKeyCallback

```
glfwSetKeyCallback(window,key_callback);        //设置键盘回调函数
```

设置键盘的回调函数。

#### glfwSetCursorPosCallback

```c++
glfwSetCursorPosCallback(window,mouse_callback);    //设置鼠标回调函数
```

设置鼠标回调函数。

#### glfwSetInputMode

输出模式。

```c++
glfwSetInputMode(window,GLFW_CURSOR,GLFW_CURSOR_DISABLED);  //捕捉窗口鼠标
```



## glm
#### glm::ortho
创建正射投影矩阵
```
glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);
```
前两个参数指定了平截头体的左右坐标，第三和第四参数指定了平截头体的底部和上部。通过这四个参数我们定义了近平面和远平面的大小，然后第五和第六个参数则定义了近平面和远平面的距离。这个指定的投影矩阵将处于这些x，y，z范围之间的坐标转换到标准化设备坐标系中。

#### glm::perspective
创建一个透视投影矩阵
```
glm::mat4 proj = glm::perspective(45.0f, (float)width/(float)height, 0.1f, 100.0f);
```
参数：
1. 定义fov值,表示视野,这里设定45.0f
2. 第二个参数设置宽高比，由视口的高除以宽。
3. 第三和第四个参数设置了截头体的近和远平面。

#### glm::radians
```
glm::radians(45.0);
```
角度转弧度。

## GLSL

#### uniform

定义一个变量，可在glsl外使用glGetUniformLocation()读取赋值。

#### clamp

把一个参数固定在0.0-1.0之间。用于保证一些值不会超出[0,1]以外。

#### normalize

法线归一化

####     reflect

```
reflect(-lightDir,norm);
```

反射，第一个参数是从光源指向片段位置的向量。第二个参数要求是一个法向量。

#### transpose

#### texture

纹理函数

#### inverse

反转

#### sampler2D

2D采样器。

#### max

求最大值

#### dot

矩阵的点乘。

#### pow

求幂函数

#### mat3

3X3矩阵。
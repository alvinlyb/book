#  SDL

## function

#### SDL_Init

```c++
SDL_Init(SDL_INIT_EVERYTHING);
```

调用这个函数能初始化SDL的所有子系统，然后我们可以开始使用SDL的图形函数。

#### SDL_CreateWindow

```c++
//设置窗口
SDL_Window *screen = SDL_CreateWindow("My Game Window",
                          SDL_WINDOWPOS_UNDEFINED,
                          SDL_WINDOWPOS_UNDEFINED,
                          640, 480,
                          SDL_WINDOW_FULLSCREEN | SDL_WINDOW_OPENGL);
```

用来建立一个640像素宽、480像素高的32位（位/像素）窗口.

此函数替换了老旧的SDL_SetVideoMode允许你创建多个窗口。

#### SDL_LoadBMP

```c++
//加载图像
hello = SDL_LoadBMP( "hello.bmp" );
```



来加载图片。这个函数需要一个指向位图文件的路径作为参数，并返回一个指向加载好的表面的指针。如果在加载过程中发生了错误，这个函数会返回`NULL`。

####  SDL_BlitSurface

```c++
 //将图像应用到窗口上
  SDL_BlitSurface( hello, NULL, screen, NULL );
```

把加载好的图像应用到窗口上。其中第一个参数是源表面，第三个参数是目的表面，它的功能是将源表面粘贴到目的表面上。 在这个程序里，它是将加载好的图像应用到窗口上。

#### SDL_CreateRenderer

```c++

SDL_CreateRenderer(screen, -1, 0);
```



#### SDL_Delay

```
SDL_Delay(2000);
```

让窗口停留一会儿。这里，我们让窗口延时2000毫秒（即2秒）。

#### SDL_FreeSurface

```c++
//释放已加载的图像
SDL_FreeSurface( hello );
```

从内存中移除。你不能仅仅使用`delete`，你必须使用`SDL_FreeSurface()`来从内存中移除图像。

#### SDL_Quit

```c++
SDL_Quit()
```
退出关闭SDL，将会删除窗口表面。

#### SDL_GL_SetAttribute

设置openGL相关属性

#### SDL_GetWindowSurface

创建surface

#### 





























































>>>>>>> .theirs

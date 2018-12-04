## OCCT
#### gp_Pnt

```
gp_Pnt P1(0,0,0);
gp_Pnt P2(5,0,0);
```

表示一个顶点.

#### gp_Vec

```
gp_Vec v1(P1,P2);
```

表示一个向量,可以用两个顶点来生成一个向量。向量有一个方法IsOpposite()，可以用来测试两个向量的方向是相对还是平行。

```
gp_Pnt P3(-5,0,2);
gp_Vec V2 (P1,P3);                                          
Standard_Boolean result =V1.IsOpposite(V2,Precision::Angular());
```

Standard_Real Magnitude() const;计算向量的大小；

tandard_Real SquareMagnitude() const;计算向量的平方；

#### gp_Dir



## TopoDS_Shape

![TopoDs_Shape](https://img-blog.csdn.net/20180821080535599?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5nYmFuZ3FpbmcxMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### TCollection_AsciiString

```c++
TCollection_AsciiString hello("hello world");
```

定义occt字符串。

#### gp_point

```
gp_point(0,0,0)
```

三维坐标中构建一个点。

使用gp_point::x()， gp_point::Y()， gp_point::Z()。可以取得已知点的X、Y、Z坐标。

gp_point::SetX(); gp_point::SetY(); gp_point::SetZ(); gp_point::SetCoord(); 可以设置三维点的坐标

#### gp_Circ

```
gp_Circ::gp_Circ(const gp_Ax2& A2, const Standard_Real Radius);
```

中心轴和半径构建圆。

#### BRepPrimAPI_MakeBox

创建矩形box

#### BRepPrimAPI_MakeWedge

创建楔形体（楔形体就是带斜面的长方体，即带角度的长方体。）

#### BRepPrimAPI_MakePrism

```c++
TopoDS_Face bottomFace = BRepBuilderAPI_MakeFace(wireProfile);
gp_Vec prismVec(0, 0, height);
TopoDS_Shape prismShape = BRepPrimAPI_MakePrism(bottomFace, prismVec);
```

拉伸面，返回的是TopoDS_Shape类型。

### TopoDS_Wire

#### BRepBuilderAPI_MakeWire

创建线

#### BRepBuilderAPI_MakeWire::Add

 添加线

#### BRepBuilderAPI_MakeWire::IsDone

判断添加边是否有效。

#### BRepBuilderAPI_MakeWire::Error

返回构建结果是否有效。

#### BRepBuilderAPI_MakeWire::Wire

返回构建的网格。

#### BRepBuilderAPI_MakeWire::Edge

返回构建网格的最后一边（与原始边可能不同）。

#### BRepBuilderAPI_MakeWire::Vertex

返回构建网格的最后一边的顶点。

### TopoDS_Face

**基类** 面

创建面,可通过gp_Pln、gp_Cylinder、gp_Cone、gp_Sphere、gp_Torus等构造面

#### BRepBuilderAPI_MakeFace

```c++
TopoDS_Face bottomFace = BRepBuilderAPI_MakeFace(wireProfile);
```

生成面。返回的是TopoDS_Face类型。

#### BRepBuilderAPI_MakeFace::Add

添加线

#### BRepBuilderAPI_MakeFace::IsDone

构成一个有效面刚返回true

#### BRepBuilderAPI_MakeFace::Error

返回构建结果状态。

#### BRepBuilderAPI_MakeFace::Face

返回构建的面。

### TopoDS_Edge

#### BRepBuilderAPI_MakeEdge

```
BRepBuilderAPI_MakeEdge(const gp_Pnt& P1, const gp_Pnt& P2);
```

通过两个点构造边。

```
BRepBuilderAPI_MakeEdge(const gp_Lin& L); //通过射线构造边
```

此外gp_Circ（圆）、gp_Elips（椭圆）、gp_Hypr（双曲线一支）、gp_Parab（抛物线）、Geom_Curve（弧线）、Geom2d_Curve（二维弧线）等亦可构建边

### BRepPrimAPI_MakeOneAxis

![BRepPrimAPI_MakeOneAxis](http://images.cppblog.com/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image012_2.jpg)

**基类** 创建旋转体。

#### BRepPrimAPI_MakeCylinder

创建圆柱体

#### BRepPrimAPI_MakeCone

创建圆锥体

#### BRepPrimAPI_MakeSphere

```c++
TopoDS_Shape sphere=BRepPrimAPI_MakeSphere(5);
```

创建球体

#### BRepPrimAPI_MakeTorus

```c++
TopoDS_Shape aTopoElbow = BRepPrimAPI_MakeTorus(anAxis, 3.0, 1.0, M_PI_2).Shape();
```

创建圆环体

#### BRepPrimAPI_MakeRevolution

创建旋转体

### BRepPrimAPI_MakeSweep

![](http://images.cppblog.com/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image036_2.jpg)

**基类** 创建扫掠体

#### BRepOffsetAPI_MakePipe 
创建管道

#### BRepOffsetAPI_MakePipeShell



#### BRepPrimAPI_MakePrism
创建拉伸体

#### BRepPrimAPI_MakeRevol
创建旋转体

### BRepAlgoAPI_BooleanOperation

![](https://img-blog.csdn.net/20180821080535721?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5nYmFuZ3FpbmcxMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### BRepAlgoAPI_Fuse

布尔并运算

#### BRepAlgoAPI_Common

布尔交运算。

#### BRepAlgoAPI_Cut

布尔差运算。

#### BRepAlgoAPI_Section

剖面。

### gp_Trsf

几何变换

#### gp_Trsf::SetMirror

镜像变换

#### gp_Trsf::SetRotation

角度旋转变换

#### gp_Trsf::SetScale

缩放变换

#### gp_Trsf::SetTranslation

平移变换

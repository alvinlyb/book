## OCCT
**OpenCASCADE**由七个模块组成，分别如下：

- **Module FoundationClasses**; 基础类；

- **Module ModelingData**; 造型数据；

- **Module ModelingAlgorihtms**; 造型算法；

- **Module Visualization**; 可视化；

- **Module ApplicationFramework**; 程序框架；

- **Module DataExchange**; 数据交换；

- **Module Draw**;

其中基础类模块有三个工具箱：

  - **Toolkit TKAdvTools**; 高级工具箱；
  - **Toolkit TKMath**; 数学工具箱；
  - **Toolkit TKernel**; 核心工具箱；

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

方向

### Bnd

#### Bnd_Box2d

定义一个二维空间的边界盒,可以得出边界盒各个点的值，有时，在某个方向是无限大,这种情况下，称为在此方向上是开放的。

```c++
Bnd_Box2d aCBox;                                                   
Geom2dAdaptor_Curve GACC (C);                                      
BndLib_Add2dCurve::Add (GACC,Precision::Approximation(),aCBox);
```

#### Bnd_Box

定义一个三维空间的边界盒,可以扩大或缩小边界盒,也可以合并两个轴对齐边界盒。

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

创建矩形box,如果以一个shell或solid返回。创建box方式有如下四种:



![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image004_thumb.jpg)
以上创建方式对应类的BRepPrimAPI_MakeBox的四种构造函数：

```c++
Standard_EXPORT   BRepPrimAPI_MakeBox(const Standard_Real dx,const Standard_Real dy,const Standard_Real dz);
Standard_EXPORT   BRepPrimAPI_MakeBox(const gp_Pnt& P,const Standard_Real dx,const Standard_Real dy,const Standard_Real dz);
Standard_EXPORT   BRepPrimAPI_MakeBox(const gp_Pnt& P1,const gp_Pnt& P2);
 
Standard_EXPORT   BRepPrimAPI_MakeBox(const gp_Ax2& Axes,const Standard_Real dx,const Standard_Real dy,const Standard_Real dz);
```

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image006_thumb.jpg)

与上图对应的创建长方体的代码如下所示：

```c++
BRepPrimAPI_MakeBox (200.,150.,100.);
BRepPrimAPI_MakeBox (gp_Pnt(60, 60, 0), 200.,150.,100.);
BRepPrimAPI_MakeBox (gp_Pnt(60, 60, 0), gp_Pnt(200.,150.,100.));
BRepPrimAPI_MakeBox (gp_Ax2(gp_Pnt(100.,80.,70.),gp_Dir(1.,2.,1.)),80.,90.,120.);
```

#### BRepPrimAPI_MakeWedge

创建楔形体（楔形体就是带斜面的长方体，即带角度的长方体。）楔形体的创建方式与长方体的创建方式相似。下图所示为构造楔形体的两种方式。一种是加入**ltx**尺寸来描述**dy**上的面，另一种是加入**xmin**，**xmax**，**zmin**，**zmax**来描述**dy**上的面。

第一种方式是第二种方式在满足以下条件下的特例：

**xmin** = **0**, **xmax** = **ltx**, **zmin** = **0**, **zmax** = **dz**

创建一个金字塔的楔形体可以用下面的方式：

**xmin** = **xmax** = **dx** / **2**, **zmin** = **zmax** = **dz** / **2**

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image008_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image010_thumb.jpg)

生成上图所示的程序如下所示：

```c++
TopoDS_Shape S1 = BRepPrimAPI_MakeWedge(60.,100.,80.,20.); 
TopoDS_Shape S2 = BRepPrimAPI_MakeWedge(gp_Ax2(gp_Pnt(100.,100.,0.),gp_Dir(0.,0.,1.)), 
                                    60.,50.,80.,25.,-10.,40.,70.);
```



#### BRepPrimAPI_MakeFillet

```c++
TopoDS_Face bottomFace = BRepBuilderAPI_MakeFace(wireProfile);
gp_Vec prismVec(0, 0, height);
TopoDS_Shape prismShape = BRepPrimAPI_MakePrism(bottomFace, prismVec);
```

倒角，为形状添加倒圆。倒圆是用光滑面来代替角边。使用方法如下：

* 首先，给定一个需要倒圆的形状；

* 然后，通过**Add**方法来添加描述倒圆的参数，倒圆所需的参数包括一个边**edge**和半径**radius**。当然，边**edge**必须由两个面**face**所共有。倒圆会将原来的边替换成光滑的圆面过渡。

* 最后，通过询问结果来执行倒圆操作。

> 注：添加一个倒圆两次并不会出错，因为只保留了最后一次添的倒圆。

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/e7e7c4790bb6_11001/clip_image002_thumb.jpg)

上图使用两个边进行倒角r1和倒角r2结果如右边所示。

下面给出一个将创建一个倒圆角的长方体，其尺寸分别为a,b,c,倒角半径r。

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/e7e7c4790bb6_11001/clip_image004_thumb.jpg)

代码如下所示，创建上图所示的倒圆的长方体的参数分别为：

**a** = **100**，**b** = **60**，**c** = **80**，**r** = **10**：

```c++
#include <TopoDS_Shape.hxx> 
#include <TopoDS.hxx> 
#include <BRepPrimAPI_MakeBox.hxx> 
#include <TopoDS_Solid.hxx> 
#include <BRepFilletAPI_MakeFillet.hxx> 
#include <TopExp_Explorer.hxx> 
 
TopoDS_Shape FilletedBox(const Standard_Real a, 
      const Standard_Real b, 
      const Standard_Real c, 
      const Standard_Real r) 
{ 
    TopoDS_Solid Box = BRepPrimAPI_MakeBox(a,b,c); 
    BRepFilletAPI_MakeFillet MF(Box); 
 
    // add all the edges to fillet 
    TopExp_Explorer ex(Box,TopAbs_EDGE); 
    while (ex.More()) 
    { 
        MF.Add(r,TopoDS::Edge(ex.Current())); 
        ex.Next(); 
    }
 
    return MF.Shape(); 
} 
```

如下图所示为创建一个半径变化的倒圆操作：

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/e7e7c4790bb6_11001/clip_image006_thumb.jpg)

**Figure** **3**. **Evolutive** **radius** **fillet**

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/e7e7c4790bb6_11001/clip_image008_thumb.jpg)

**Figure** **4**. **Evolutive** **radius** **fillet** **a** **box**

程序代码如下所示：

```c++
TopoDS_Shape theBox = BRepPrimAPI_MakeBox(200, 200, 200);
BRepFilletAPI_MakeFillet Rake(theBox);
ChFi3d_FilletShape FSH = ChFi3d_Rational;
Rake.SetFilletShape(FSH);

TColgp_Array1OfPnt2d parAndRad(1, 6);
parAndRad.SetValue(1, gp_Pnt2d(0, 10));
parAndRad.SetValue(2, gp_Pnt2d(50, 20));
parAndRad.SetValue(3, gp_Pnt2d(70, 20));
parAndRad.SetValue(4, gp_Pnt2d(130, 60));
parAndRad.SetValue(5, gp_Pnt2d(160, 30));
parAndRad.SetValue(6, gp_Pnt2d(200, 20));

TopExp_Explorer ex(theBox, TopAbs_EDGE);
Rake.Add(parAndRad, TopoDS::Edge(ex.Current()));
TopoDS_Shape evolvedBox = Rake.Shape();
```

#### BRepPrimAPI_MakeFillet2d

我按照示例代码运行了一下程序，结果程序总是崩溃，其操作的效果不得而知，所以也得不到真实的效果图。将其程序代码列出如下所示：

> 我想应该是TopExp_Explorer的问题吧，顶点构成边时共享使用了多次，改用TopExp::MapShapes(srcFace,TopAbs_VERTEX,mapVertex);即可。

````c++
#include “BRepPrimAPI_MakeBox.hxx” 
#include “TopoDS_Shape.hxx” 
#include “TopExp_Explorer.hxx” 
#include “BRepFilletAPI_MakeFillet2d.hxx” 
#include “TopoDS.hxx” 
#include “TopoDS_Solid.hxx” 
 
TopoDS_Shape FilletFace(const Standard_Real a, 
     const Standard_Real b, 
     const Standard_Real c, 
     const Standard_Real r) 
 { 
     TopoDS_Solid Box = BRepPrimAPI_MakeBox (a,b,c); 
     TopExp_Explorer ex1(Box,TopAbs_FACE); 
  
     const TopoDS_Face& F = TopoDS::Face(ex1.Current()); 
     BRepFilletAPI_MakeFillet2d MF(F); 
     TopExp_Explorer ex2(F, TopAbs_VERTEX); 
  
     while (ex2.More()) 
     { 
         MF.AddFillet(TopoDS::Vertex(ex2.Current()),r); 
         ex2.Next(); 
     } 
  
     // while... 
     return MF.Shape(); 
 }
````

#### BRepPrimAPI_MakeChamfer

倒角，使用方法与BRepPrimAPI_MakeFille大致类似。稍有不同：

* The surfaces created are ruled and not smooth;

* The Add syntax for selecting edges requires one or two distances, one edge and one face(contiguous to the edge);

* Add(dist, E, F);

* Add(d1, d2, E, F); with d1 on the face F.

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/e7e7c4790bb6_11001/clip_image010_thumb.jpg)

**Figure** **5**. **Creating** **a** **chamfer**

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/e7e7c4790bb6_11001/clip_image012_thumb.jpg)

**Figure** **6**. **The** **box** **with** **chamfers**

程序代码如下所示：

```c++
TopoDS_Shape theBox = BRepPrimAPI_MakeBox(130,200,170); 
BRepFilletAPI_MakeChamfer MC(theBox); 
TopTools_IndexedDataMapOfShapeListOfShape M; 
TopExp::MapShapesAndAncestors(theBox,TopAbs_EDGE,TopAbs_FACE,M); 
 
for (Standar1d_Integer i;i<M.Extent();i++) 
{ 
   TopoDS_Edge E = TopoDS::Edge(M.FindKey(i)); 
   TopoDS_Face F = TopoDS::Face(M.FindFromIndex(i).First()); 
   MC.Add(15,15,E,F); 
} 
 
TopoDS_Shape ChanfrenedBox = MC.Shape(); 
```

#### BRepMesh_IncrementalMesh

```c++
BRepMesh_IncrementalMesh(m_TopoFace, aDeflection);
```

根据正确的三角剖分建立一个形状的网格。

### TopoDS_TShape

#### TopoDS_TCompound

#### TopoDS_TCompSolid

#### TopoDS_TEdge

##### BRep_TEdge

#### TopoDS_TFace

##### BRep_TFace

#### TopoDS_TShell

#### TopoDS_TSolid

#### TopoDS_TVertex

##### BRep_TVertex

#### TopoDS_TWire

### TopoDS_Wire

#### BRepBuilderAPI_MakeWire

创建线框，以下用一个wire和一个边来生成一新的wire:

```c++
ExistingWire = BRepBuilderAPI_MakeWire(Edge2);
Edge3 = BRepBuilderAPI_MakeEdge(gp_Pnt(-300,0,-80),gp_Pnt(-90,20,-30));
BRepBuilderAPI_MakeWire MW1(ExistingWire,Edge3);
if (MW1.IsDone()) {YellowWire = MW1;}
```

#### BRepBuilderAPI_MakeWire::Add

 添加线,下面用一个wire和添加边的方法生成wire

```c++
BRepBuilderAPI_MakeWire MW;
MW.Add(ExistingWire2);
MW.Add(Edge5);
MW.Add(Edge6);
MW.Add(Edge7);
if (MW.IsDone()) {
	WhiteWire = MW.Wire();
    LastEdge = MW.Edge();
	 LastVertex = MW.Vertex();
}
```

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

##### BRepBuilderAPI_MakeFace::Add

添加线

##### BRepBuilderAPI_MakeFace::IsDone

构成一个有效面刚返回true

##### BRepBuilderAPI_MakeFace::Error

返回构建结果状态。

##### BRepBuilderAPI_MakeFace::Face

返回构建的面。

#### BRepBuilderAPI_MakePolygon

创建一个polygonal wires，可以通过一组点或向量生成，也可以先生成一个空的对象，再添加点。

示例1：

```c++
BRepBuilderAPI_MakePolygon P;
P.Add(gp_Pnt(0.,0.,0.));
P.Add(gp_Pnt(200.,0.,0.));
P.Add(gp_Pnt(200.,200.,0.));
P.Add(gp_Pnt(0.,200.,0.));
P.Add(gp_Pnt(0.,0.,0.));
TopoDS_Wire W = P.Wire();
```

示例2：

```c++
TopoDS_Wire wprof = BRepBuilderAPI_MakePolygon(gp_Pnt(0.,0.,0.),gp_Pnt(-60.,-60.,-200.));
```



### TopoDS_Edge

#### BRepBuilderAPI_MakeEdge

```c++
BRepBuilderAPI_MakeEdge(const gp_Pnt& P1, const gp_Pnt& P2);
```

通过两个点构造边。

```c++
BRepBuilderAPI_MakeEdge(const gp_Lin& L); //通过射线构造边

gp_Elips Elips(gp_Ax2(gp_Pnt(10,0,0),gp_Dir(1,1,1)),60,30);
RedEdge = BRepBuilderAPI_MakeEdge(Elips,0,PI/2);	//生成四分一圆边
```

此外gp_Circ（圆）、gp_Elips（椭圆）、gp_Hypr（双曲线一支）、gp_Parab（抛物线）、Geom_Curve（弧线）、Geom2d_Curve（二维弧线）等亦可构建边

### BRepPrimAPI_MakeOneAxis

![BRepPrimAPI_MakeOneAxis](http://images.cppblog.com/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image012_2.jpg)

**基类** 所有创建旋转体的基类。旋转体通过将曲线绕一个轴旋转得到。它们有圆柱，圆锥体，球体，圆环体等。这些旋转体对于不同的图元有不同的构造参数，但是穹们也有共同的参数。

* 一个坐标系统；
* 一个角度，范围为[0,2*pi]:
* 曲线的vmin,vmax参数;

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image014_thumb.jpg)

OneAxis构造的结果是solid,shell或face。通常不会直接使用BRepPrimAPI_MakeOneAxis而是使用它的派生类。

#### BRepPrimAPI_MakeCylinder

创建圆柱体,圆柱体可以在默认的坐标系统中被创建，也可以指定的坐标系统(gp_Ax2)中创建。创建方式如下：

* 半径，高度创建一个完整的圆柱；
* 半径，高度和角度来创建部分圆柱；

如下代码所示为沿x,y,z偏移一定距离的四分之一的圆柱：

```c++
Standard_Real X = 20;
Standard_Real Y = 10;
Standard_Real Z = 15;
Standard_Real R = 10;
Standard_Real DY = 30;
// Make the system of coordinates.
gp_Ax2 axes = gp::ZOX();
axes.Translate(gp_Vec(X, Y, Z));
TopoDS_Face F = BRepPrimAPI_MakeCylinder(axes, R, DY, PI/2.);
```

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image016_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image018_thumb.jpg)

#### BRepPrimAPI_MakeCone

创建圆锥体,与圆柱体一样，圆锥体可以在默认的坐标系中被创建，也可以指定的坐标系中被创建。创建方式如下：

* 两个半径和高度来创建一个完整的圆锥。一个半径可以为零，这样就创建了一个尖顶的圆锥；

* 半径、高度和角度来创建部分圆锥。

如下代码创建的圆锥如下图所示：

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image020_thumb.jpg)

```c++
TopoDS_Shape C1 = BRepPrimAPI_MakeCone (50.,25.,200.); 
TopoDS_Shape C2 = BRepPrimAPI_MakeCone(gp_Ax2(gp_Pnt(100.,100.,0.), 
                                       gp_Dir(0.,0.,1.)), 605.,0.,150.,210.*PI180); 
```



#### BRepPrimAPI_MakeSphere

```c++
TopoDS_Shape sphere=BRepPrimAPI_MakeSphere(5);
```

创建球体,与圆柱体一样，球体可以在默认的坐标系中被创建，也可以指定的坐标系中被创建。有四种创建方式，如下图所示：

* 一个半径值，创建一个完整的球体；

* 半径和角度，创建部分的球体；

* 半径和两个角度值，创建纬度上的部分的球体。两个角度**a1**，**a2**必须满足如下条件：**PI**/**2** <= **a1** < **a2** <= **PI**/**2**

* 一个半径和三个角度值；

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image022_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image024_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image026_thumb.jpg)

```c++
TopoDS_Shape S1 = BRepPrimAPI_MakeSphere(gp_Pnt(-200.,-250.,0.),80.); 
TopoDS_Shape S2 = BRepPrimAPI_MakeSphere(100.,120.*PI180); 
TopoDS_Shape S3 = BRepPrimAPI_MakeSphere(gp_Pnt(200.,250.,0.),100., 
                                     -60.*PI180, 60.*PI180); 
TopoDS_Shape S4 = BRepPrimAPI_MakeSphere(gp_Pnt(0.,0.,-300.),150., 
                                     -45.*PI180, 45.*PI180, 45.*PI180);
```

##### BRepOffsetAPI_ThruSections

此类继承自BRepBuilderAPI_MakeShape：创建一个loft,通过一组给定的sections，生成一个shell或一个solid;通常，section是wire;但是第一个和最后一个section可以是vertices。

```
BRepOffsetAPI_ThruSections generator(Standard_False,Standard_True);
generator.AddWire(W1);
generator.AddWire(W2);
generator.AddWire(W3);
generator.AddWire(W4);
generator.Build();
TopoDS_Shape S1 = generator.Shape();
Handle(AIS_Shape) ais1 = new AIS_Shape(S1);
```

#### BRepPrimAPI_MakeTorus

```c++
TopoDS_Shape aTopoElbow = BRepPrimAPI_MakeTorus(anAxis, 3.0, 1.0, M_PI_2).Shape();
```

创建圆环体,与其它基本图元类似，一个圆环体可以在默认的坐标系中创建，也可以在指定的坐标系中创建。创建与与球体创建方式类似：

* 两个半径，创建一个完整的圆环体；

* 两个半径和一个角度值，创建部分的圆环体；

* 两个半径和两个角度值，创建纬度上的部分圆环体；两个角度必须满足如下条件：**0** < **a2** – **a1** < **2** * **PI**；

* 两个半径和三个角度值，创建部分的圆环体；

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image028_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image030_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image032_thumb.jpg)

```c++
TopoDS_Shape S1 = BRepPrimAPI_MakeTorus(60.,20.); 
TopoDS_Shape S2 = BRepPrimAPI_MakeTorus(gp_Ax2(gp_Pnt(100.,100.,0.),gp_Dir(1.,1.,1.)), 
                                    50.,20.,210.*PI180); 
TopoDS_Shape S3 = BRepPrimAPI_MakeTorus(gp_Ax2(gp_Pnt(-200.,-150.,-100),gp_Dir(0.,1.,0.)), 
                                    60.,20.,-45.*PI180,45.*PI180,90.*PI180);
```

#### BRepPrimAPI_MakeRevolution

创建旋转体,根据一条曲线和轴创建旋转体。曲线可以是任意的**Geom**_**Curve**，有四种构造方式：

* From a curve, use the full curve and make a full rotation;

* From a curve and an angle of rotation;

* From a curve and two parameters to trim the curve. The two parameters must be growing and within the curve range;

* From a curve, two parameters, and an angle. The two parameters must be growing and within the curve range.

### BRepPrimAPI_MakeSweep

![](http://images.cppblog.com/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image036_2.jpg)

**基类** 扫掠体（**Sweeps**）就是一条截面（**profile**）沿任意轨道线（**path**）扫掠后得到的体。截面可以是任意的拓朴结构。轨道线通常是曲线（**curve** **or** **wire**）。创建扫掠体的规则如下：

* 点扫掠后得到边；**Vertices** **generate** **Edges**;

* 边扫掠后得到面；**Edges** **generate** **Faces**;

* 线扫掠后得到壳；**Wires** **generate** **Shells**;

* 面扫掠后得到体；**Faces** **generate** **Solids**;

* 壳扫掠后得到组合体；**Shells** **generate** **Composite** **Solids**;

* A Compound generates a Compound with the sweep of all its elements;

(Solid)和组合体（Composite Solids）的扫掠是禁止的。

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image034_thumb.jpg)

包BRepPrimAPI中实现了三种扫掠体：称线性扫掠体为Prism，称旋转扫掠体为Revol和通用的扫掠体叫作Pipe。

#### BRepOffsetAPI_MakePipe 

创建管道，类继承关系是：BRepBuilderAPI_MakeShape--〉BRepPrimAPI_MakeSweep-->BRepOffsetAPI_MakePipe

以下为生成一个管道的示例过程：

--利用生成一个WIRE，作为管道的路径:

```
Handle(Geom_BezierCurve) curve = new Geom_BezierCurve(CurvePoles);
TopoDS_Edge E = BRepBuilderAPI_MakeEdge(curve);
TopoDS_Wire W = BRepBuilderAPI_MakeWire(E);
```
生成一个面，作为生成管道的截面：
```
gp_Circ c = gp_Circ(gp_Ax2(gp_Pnt(0.,0.,0.),gp_Dir(0.,1.,0.)),10.);
TopoDS_Edge Ec = BRepBuilderAPI_MakeEdge(c);
TopoDS_Wire Wc = BRepBuilderAPI_MakeWire(Ec);
TopoDS_Face F = BRepBuilderAPI_MakeFace(gp_Pln(gp::ZOX()),Wc);
```
--利用前两步生成的路径和截面来生成pipe:

```
TopoDS_Shape S = BRepOffsetAPI_MakePipe(W,F);
Handle(AIS_Shape) ais2 = new AIS_Shape(S);
```

#### BRepOffsetAPI_MakePipeShell

通用的扫掠算法使用类BRepOffAPI_MakePipeShell，在Draw Test Harness中使用如下命令：

* mksweep

* addsweep

* deletesweep

* sestsweep

* buildsweep

其中setsweep命令有如下选项：

```
setsweep options [arg1 [arg2 [...]]] : options are :
   -FR : Tangent and Normal are given by Frenet trihedron
   -CF : Tangente is given by Frenet, the Normal is computed to minimize the torsion
   -DT : discrete trihedron
   -DX Surf : Tangent and Normal are given by Darboux trihedron,
       Surf have to be a shell or a face
   -CN dx dy dz : BiNormal is given by dx dy dz
   -FX Tx Ty TZ [Nx Ny Nz] : Tangent and Normal are fixed
   -G guide  0|1(Plan|ACR)  0|1|2(no contact|contact|contact on border) : with guide
```

-FR选项：切矢和法向由Frenet标架确定；

-CF选项：切矢由Frenet标架指定，法向通过计算最小扭转来确定；

-DT选项：切矢和法向由Darboux标架确定；

-CN选项：副法向由指定的dx, dy, dz确定；

-FT：切矢和法向是固定的；

下面通过例子来说明setsweep中这些标架选项的原理。

```
polyline p  0 0 0 1 0 0 1 2 0 0 1 0
circle c 0 0 0 1 0 0 0.2
mkedge e c
wire w e
mksweep p
addsweep w
setsweep -FX 1 0 0
buildsweep r -C
vdisplay p w r
```

运行上述Draw Test Harness脚本得到如下图所示模型：

![](https://images2018.cnblogs.com/blog/534255/201804/534255-20180426211454856-1389581282.png)

通过setsweep -FX 1 0 0 使轮廓Profile沿着扫掠路径Path移动时，法向始终为X轴方向。

将setsweep设置为FR，即使Profile沿着Path移动时采用Frenet标架，更改后的脚本如下：

```
mksweep p
addsweep w
setsweep -FR
buildsweep r -C
vdisplay p w r
```

![](https://images2018.cnblogs.com/blog/534255/201804/534255-20180426211456236-1872053119.png)



由上图可以看出，轮廓Profile沿着路径Path移动时，轮廓的法向始终与路径平行，即与路径的切线一致。

通过上面命令的对比可以理解setsweep中的选项原理了。关于更多形式的标架，如Darboux标架之类的，可以自己搜索学习。

其中buildsweep命令有如下选项：

```
buildsweep result [-M/-C/-R] [-S] [tol] : options are
   -M : Discontinuities are treated by Modfication of
        the sweeping mode : it is the default
   -C : Discontinuities are treated like Right Corner
        Treatement is Extent && Intersect
   -R : Discontinuities are treated like Round Corner
        Treatement is Intersect and Fill
   -S : To build a Solid
```

buildsweep有指定不连续的处理方式及是否生成实体。其中

n -C：将路径Path中不连续的地方通过延长和相交进行处理；

n -R：将路径Path中不连续的地方通过相交和填充进行处理；

下面通过例子来说明buildsweep中选项的用法。通过运行下面的Draw 脚本：

```
polyline p  0 0 0 1 0 0 1 2 0 0 1 0 0 0 0
circle c 0 0 0 1 0 0 0.2
mkedge e c
wire w e
mksweep p
addsweep w
buildsweep r -C
vdisplay p w r
```

在buildsweep时使用－C选项，得到效果如下图所示：

![](https://images2018.cnblogs.com/blog/534255/201804/534255-20180426211457048-199401715.png)

将上述脚本中更改一下buildsweep的选项，由－C改为－R，即：

```
buildsweep r -R
```

得到的效果如下图所示：

![](https://images2018.cnblogs.com/blog/534255/201804/534255-20180426211457864-1685657834.png)

由上面两个结果对比可以理解buildsweep的-C和-R选项了。

#### BRepOffsetAPI_MakeEvolved

创建一个可展图形，它是通过一个planar spine (face or wire)和一个rofile (wire)来生成的，它是一个非循环的sweep (pipe),用profile沿着spline;自相交点将被移除；

比如：--沿着一个spline，sweep一个profile;

```c++
Standard_EXPORT BRepOffsetAPI_MakeEvolved(const TopoDS_Face& Spine,const TopoDS_Wire& Profil,const GeomAbs_JoinType Join = GeomAbs_Arc,const Standard_Boolean
AxeProf = Standard_True,const Standard_Boolean Solid = Standard_False,const Standard_Boolean ProfOnSpine = Standard_False,const Standard_Real Tol = 0.0000001);
```

AxeProf参数如果为true,R是0,X,Y,Z;如果solid为真，结果为一个solid或复合的solids;

示例：
```c++
TopoDS_Shape S = BRepOffsetAPI_MakeEvolved(W,wprof,GeomAbs_Arc,Standard_True,Standard_False,Standard_True,0.0001);
```
#### BRepOffsetAPI_DraftAngle

创建一个tapered图形；一般过程是：

* 初始化构造算法；

* 输入要taper的特征面；

* 实现算法；

* 生成结果；

示例：
```c++
TopoDS_Shape S = BRepPrimAPI_MakeBox(200.,300.,150.);
BRepOffsetAPI_DraftAngle adraft(S);
TopExp_Explorer Ex;
for (Ex.Init(S,TopAbs_FACE); Ex.More(); Ex.Next()) {
	TopoDS_Face F = TopoDS::Face(Ex.Current());
	Handle(Geom_Plane) surf = Handle(Geom_Plane)::DownCast(BRep_Tool::Surface(F));
    gp_Pln apln = surf->Pln();
	gp_Dir dirF = apln.Axis().Direction();
    if (dirF.IsNormal(gp_Dir(0.,0.,1.),Precision::Angular()))
		adraft.Add(F, gp_Dir(0.,0.,1.), 15.*PI180, gp_Pln(gp::XOY()));
}
ais1->Set(adraft.Shape());
```
#### BRepPrimAPI_MakePrism

```c++
TopoDS_Face bottomFace = BRepBuilderAPI_MakeFace(wireProfile);
gp_Vec prismVec(0, 0, height);
TopoDS_Shape prismShape = BRepPrimAPI_MakePrism(bottomFace, prismVec);
```

创建拉伸体，创建线性拉伸体prism。拉伸体可从一个形状及一个向量或一个方向来创建。从一个向量可以创建一个有限拉伸体，而从一个方向可以创建一个无限的或半无限的拉伸体。一个布尔的函数参数用来控制半无限和全无限的拉伸体。所有的构造函数都有一个布尔值来控制构造的拉伸体与原拉伸体是否共享，默认是共享方式构造。如下代码为使用一个面，一个方向和一个长度来创建一个有限的、无限的和半无限的拉伸体。

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image038_thumb.jpg)
![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image040_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image042_thumb.jpg)

生成上图所示形状的代码如下所示：
```c++
//--- Prism a vertex -> result is an edge --- 
TopoDS_Vertex V1 = BRepBuilderAPI_MakeVertex(gp_Pnt(-200.,-200.,0.)); 
TopoDS_Shape S1 = BRepBuilderAPI_MakePrism(V1,gp_Vec(0.,0.,100.)); 
//--- Prism an edge -> result is a face --- 
TopoDS_Edge E = BRepBuilderAPI_MakeEdge(gp_Pnt(-150.,-150,0.), gp_Pnt(-50.,-50,0.)); 
TopoDS_Shape S2 = BRepPrimAPI_MakePrism(E,gp_Vec(0.,0.,100.)); 
//--- Prism an wire -> result is a shell --- 
TopoDS_Edge E1 = BREpBuilderAPI_MakeEdge(gp_Pnt(0.,0.,0.), gp_Pnt(50.,0.,0.)); 
TopoDS_Edge E2 = BREpBuilderAPI_MakeEdge(gp_Pnt(50.,0.,0.), gp_Pnt(50.,50.,0.)); 
TopoDS_Edge E3 = BREpBuilderAPI_MakeEdge(gp_Pnt(50.,50.,0.), gp_Pnt(0.,0.,0.)); 
TopoDS_Wire W = BRepBuilderAPI_MakeWire(E1,E2,E3); 
TopoDS_Shape S3 = BRepPrimAPI_MakePrism(W,gp_Vec(0.,0.,100.)); 
//--- Prism a face or a shell -> result is a solid --- 
gp_Circ c = gp_Circ(gp_Ax2(gp_Pnt(200.,200.,0.gp_Dir(0.,0.,1.)), 80.); 
TopoDS_Edge Ec = BRepBuilderAPI_MakeEdge(c); 
TopoDS_Wire Wc = BRepBuilderAPI_MakeWire(Ec); 
TopoDS_Face F = BRepBuilderAPI_MakeFace(gp::XOY(),Wc); 
TopoDS_Shape S4 = BRepBuilderAPI_MakePrism(F,gp_Vec(0.,0.,100.)); 
```

拉伸面，返回的是TopoDS_Shape类型。

#### BRepPrimAPI_MakeRevol

创建旋转扫掠体。旋转扫掠体由一个形状、轴和一个角度构造而得。类**BRepPrimAPI**_**MakeRevol**所有的构造函数的最后一个参数是用来控制构造的形状与原形状的关系，默认为共享的方式。

> 注：所有旋转体的角度值都是弧度值。

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image044_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image046_thumb.jpg)

![](http://www.cppblog.com/images/cppblog_com/eryar/WindowsLiveWriter/OpenCascade_13EC5/clip_image048_thumb.jpg)

生成上图所示形状的代码如下所示：

```c++
//--- Revol of a vertex -> result is an edge ---
TopoDS_Vertex V1 = BRepBuilderAPI_MakeVertex(gp_Pnt(-200.,-200.,0.));
gp_Ax1 axe = gp_Ax1(gp_Pnt(-170.,-170.,0.),gp_Dir(0.,0.,1.));
TopoDS_Shape S1 = BRepPrimAPI_MakeRevol(V1,axe);
//--- Revol of an edge -> result is a face --- 
TopoDS_Edge E = BRepBuilderAPI_MakeEdge(gp_Pnt(-120.,-120,0.), gp_Pnt(-120.,-120,100.)); 
axe = gp_Ax1(gp_Pnt(-100.,-100.,0.),gp_Dir(0.,0.,1.));
TopoDS_Shape S2 = BRepPrimAPI_MakeRevol(E,axe);
//--- Revol of a wire -> result is a shell ---
TopoDS_Edge E1 = BRepBuilderAPI_MakeEdge(gp_Pnt(0.,0.,0.), gp_Pnt(50.,0.,0.));
TopoDS_Edge E2 = BRepBuilderAPI_MakeEdge(gp_Pnt(50.,0.,0.), gp_Pnt(50.,50.,0.));
TopoDS_Edge E3 = BRepBuilderAPI_MakeEdge(gp_Pnt(50.,50.,0.), gp_Pnt(0.,0.,0.));
TopoDS_Wire W = BRepBuilderAPI_MakeWire(E1,E2,E3);
axe = gp_Ax1(gp_Pnt(0.,0.,30.),gp_Dir(0.,1.,0.));
TopoDS_Shape S3 = BRepPrimAPI_MakeRevol(W,axe, 210.*PI180);
//--- Revol of a face -> result is a solid --- 
gp_Circ c = gp_Circ(gp_Ax2(gp_Pnt(200.,200.,0.),gp_Dir(0.,0.,1.)), 80.);
TopoDS_Edge Ec = BRepBuilderAPI_MakeEdge(c);
TopoDS_Wire Wc = BRepBuilderPI_MakeWire(Ec);
TopoDS_Face F = BRepBuilderAPI_MakeFace(gp_Pln(gp::XOY()),Wc);
axe = gp_Ax1(gp_Pnt(290,290.,0.),gp_Dir(0.,1,0.));
TopoDS_Shape S4 = BRepPrimAPI_MakeRevol(F,axe, 90.*PI180); 
```

#### BRepPrimAPI_MakeWedge

生成一个楔块或楔块的一部分。



### BRepAlgoAPI_BooleanOperation

![](https://img-blog.csdn.net/20180821080535721?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1YW5nYmFuZ3FpbmcxMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### BRepAlgoAPI_Fuse

布尔并运算

#### BRepAlgoAPI_Common

布尔交运算。

#### BRepAlgoAPI_Cut

布尔差运算。

#### BRepAlgoAPI_Section

计算两个图形或几何体的截面，几何对象可以是平面的表面，转化为face.

示例：

给定两个图形S1和S2，计算在S1和S2上的边，在新曲线上生成近似值，结果在第一部分上而不在第二部分上：
```
Standard_Boolean PerformNow = Standard_False;
BRepBoolAPI_Section S(S1,S2,PerformNow);
S.ComputePCurveOn1(Standard_True);
S.Approximation(Standard_True);
S.Build();
TopoDS_Shape R = S.Shape();
```
如果结果为空，调用NotDone()；

常见方法：
```c++
BRepAlgoAPI_Section(const Handle(Geom_Surface)& Sf1,const Handle(Geom_Surface)& Sf2,const Standard_Boolean
PerformNow = Standard_True);
```
用来生成线：

* 两个图形SH1和SH2；

* 图形SH和平面P1；

* 表面SF和图形SH；

* 两个表面SF1和SF2；

参数PerformNow如果为真，将直接计算结果，如果为假，表示后面将通过Build（）这个函数来计算结果；

生成后的图形是由方法Shape（）得出的；

这些相交的边是独立的，不在一个链表上，也不在一个wire上，如果不存在一个相交边，返回结果为空；

示例：

--计算相交的基本边--利用这些基本边创建一个相交线--决定相交线在两个图形的哪个图形的参数空间；
```c++
TopoDS_Shape S1 = ... , S2 = ... ;
Standard_Boolean PerformNow = Standard_False;
BRepAlgoAPI_Section S ( S1, S2, PerformNow );
S.ComputePCurveOn1 (Standard_True);
S.Approximation (Standard_True);
S.Build();
TopoDS_Shape R = S.Shape();
```
### BRepTools

OpenCASCADE提供了一个类BRepTools，其中有许多static函数，主要用来对BRep表示的拓朴形状的数据进行读写，也提供了查找一个面中外环（Outer Wire）的函数。因为OpenCASCADE中的边界表示法BRep的数据结构如下图1.1所示：

![](http://www.cppblog.com/images/cppblog_com/eryar/Windows-Live-Writer/OpenCASCADE-BRepTools_11D6A/wps_clip_image-4070_thumb.png)

因为OpenCASCADE中拓朴结构采用了包含关系，当需要将TopoDS_Shape数据保存到文件时，如何保持TopoDS_Shape中的关系，以便于从文件读取这些数据时，可以重构出TopoDS_Shape中的各种关系？

参考opennurbs中的BRep表示时数据的存储方式，可知直接在BRep中保存拓朴及几何数据的索引，这样对数据的存储及读取时重构拓朴结构还是很方便的。而在OpenCASCADE中拓朴数据是以Handle来保存的，且为组合关系，即一个父结构中有一个列表（TopoDS_ListOfShape）给包含了子结构数据。对于没有索引的OpenCASCADE的拓朴结构，如何进行读写操作呢？

本文结合类BRepTools中的函数，对OpenCASCADE中TopoDS_Shape数据的保存和读取功能的代码进行分析，从而对ModelingData中的BRep数据做进一步的理解。

#### BRepTools::Dump

```c++
void  BRepTools::Dump(const TopoDS_Shape& Sh, Standard_OStream& S)
{
  BRepTools_ShapeSet SS;
  SS.Add(Sh);
  SS.Dump(Sh,S);
  SS.Dump(S);
}
```

#### BRepTools::Read

#### BRepTools::Write

#### BRepTools::Clean

删除<S>的所有面的三角形，并删除所有三角形的边缘多边形。

#### BRepBuilderAPI_ModifyShape

当使用BRepTools来创建一个修改类，主要有以下派生类：

1. BRepBuilderAPI_Copy:处理一个图形的拷贝；

2. BRepBuilderAPI_Transform 和BRepBuilderAPI_GTransform：用来对一个图形应用几何变形；

3. BRepBuilderAPI_NurbsConvert:用来将一个图形转化为NURBS几何体；

4. BRepOffsetAPI_DraftAngle：创建一个tapered图形；

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

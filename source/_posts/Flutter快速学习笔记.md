---
title: Flutter快速学习笔记
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-26 17:17:07
Password: 123
summary:
tags:
categories:
---

## 路由和路由管理

一个Route对应一个page，可以将Route简单理解成一个page。路由管理维护一个路由栈，管理页面之间的跳转逻辑，有push和pop等操作。

#### MaterialPageRoute

用来构建一个Page对应的Route。

```dart
MaterialPageRoute({
    WidgetBuilder builder, // 构建Widget的函数
    RouteSettings settings, // 保存路由的信息和携带的参数等
    bool maintainState = true, // 当路由不在栈顶时，是否依然保存state数据
    bool fullscreenDialog = false, // 路由是否是全屏弹窗，如果是，将会从屏幕底部向上入栈
  })
```

#### Navigator

路由管理类，主要提供入栈和出栈相关操作

```dart
Future push(BuildContext context, Route route)
bool pop(BuildContext context, [ result ])

Navigator.push(BuildContext context, Route route)
Navigator.of(context).push(Route route)
```

#### 命名路由

给Route定义一个名称，通过一个Map来管理名称对应的Route，这个Map叫做路由表。

```dart
// 注意：Map的key是Route的名称，value是Router的Builder函数，而不是路由本身。
Map<String, WidgetBuilder> routes;
```



注册路由表是在`MaterialApp`中注册，根路由的注册也是在`MaterialApp`中配置

#### 路由传值

- 构建Route对应Widget的时候传值
- push函数传值

```dart
// 方式一：
Future pushNamed(BuildContext context, String routeName,{Object arguments})
Navigator.of(context).pushNamed("new_page", arguments: "hi");
// 方式二：
routes:{
   "new_page":(context) => NewRoute(),
   "/":(context) => MyHomePage(title: 'Flutter Demo Home Page'), //注册首页路由
} 
```

- 通过ModalRoute对象获取push传值

```dart
//获取路由参数  
 var args = ModalRoute.of(context).settings.arguments;
```

#### **生成路由的钩子**

如果push一个未注册的命名路由，如登陆页对应的Route，那么钩子函数`onGenerateRoute`会被调用，`onGenerateRoute`在`MaterialApp`中配置。我们可以在这个钩子函数中处理相关处理逻辑，然后构建登录页对应的Route并返回。

```dart
onGenerateRoute:(RouteSettings settings){
      return MaterialPageRoute(builder: (context){
           String routeName = settings.name;
       // 如果访问的路由页需要登录，但当前未登录，则直接返回登录页路由，
       // 引导用户登录；其它情况则正常打开路由。
     }
   );
  }
```

## 资源管理

除了需要将资源拖入项目工程中，还需要在[`pubspec.yaml`](https://www.dartlang.org/tools/pub/pubspec)文件中配置`assets`。

- 配置assets

  ```yaml
  flutter:
    assets:
      - assets/my_icon.png
      - assets/background.png
  ```

- 加载assets

  - 全局静态的`rootBundle`对象来加载asset

  - 通过 `DefaultAssetBundle`加载

    `DefaultAssetBundle`是使父级widget在运行时动态替换的不同的AssetBundle，这对于本地化或测试场景很有用.

- 加载图片

  - 加载图片数据

    ```dart
    AssetImage('graphics/background.png')
    AssetImage('icons/heart.png', package: 'my_icons')
    ```

  - 加载图片对应的Widget

    ```dart
    Image.asset('graphics/background.png');
    ```

## 基础Widget

#### state的生命周期

- 第一次加载某个StateFullWidget时，state的生命周期

  flutter: initState // 初始化state，仅在第一次加载或者被释放后被加载后初始化时调用
  flutter: didChangeDependencies // 和当前StateFullWidget相互依赖的StateFullWidget发生变化时被调用
  flutter: build:

- 然后不做任何改动热重载

  flutter: reassemble   // 热重载和释放时被调用
  flutter: didUpdateWidget  // 热重载重组后被调用
  flutter: build:

- 只改变其中Text的字体大小和颜色，再热重载

  flutter: reassemble
  flutter: didUpdateWidget
  flutter: build:

- 插入子Widget，再热重载

  flutter: reassemble
  flutter: didUpdateWidget
  flutter: build:

- push新路由

  flutter: deactive //冻结  进入后台状态时被调用
  flutter: build:

- pop

  flutter: deactive
  flutter: build: 

- 移除fullStateWidget（把当前被打印的fullStateWidget从父Widget中替换为其他Widget），再热重载

  flutter: reassemble // 重组
  flutter: deactive  // 停用
  flutter: dispose // 释放

#### 文本和字体样式

- Text
- TextSpan
- DefaultTextStyle
- 字体设置和使用

#### 按钮

- RaiseButton

- FlatButton

- OutlineButton

- IconButtob

- 带图标Button

- 自定义Button

  

#### 图片和Icon

- Image
- Image缓存
- Icon
- 自定义字体Icon

#### 单选和复选框

#### 输入框和表单

- TextField
- Form
- FormField和FormState

#### 进度指示器

- Linea rProgressIndicator
- CircularProgressIndicator
- 自定义

## 布局Widget

> 布局类Widget一般都需要接收一个widget数组（children），他们直接或间接继承自（或包含）MultiChildRenderObjectWidget 

#### 线性布局（Row和Column）

- Row

```dart
Row({
  ...  
  // 水平方向的排列方向
  TextDirection textDirection,   
  // 在主水平方向占用的空间，子组件没有占满剩余空间，则等于所有子组件占用的的水平空间
  MainAxisSize mainAxisSize = MainAxisSize.max,  
  // 水平方向的对齐方式，mainAxisSize为min时该属性无效
  MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
  // 垂直方向的排列方向
  VerticalDirection verticalDirection = VerticalDirection.down,  
  // 垂直方向的对齐方式
  CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
  // 子组件数组
  List<Widget> children = const <Widget>[], 
})
```

- Column

Column和Row的属性一直，区别在与Column的主轴为纵轴。

如果Row嵌套Row，或者Column嵌套Column，之后最外层的Column或者Row才有能力占满最大空间。可以使用Expanded强制使内部的Row或者Column沾满最大空间。

#### 弹性布局（Flex和Expanded）

- Flex

  Flex布局允许子组件按着一定比例分配父组件的空间一般和Expanded搭配使用

  ```dart
  Flex({
    ...
     //弹性布局主轴的方向, Row默认为水平方向，Column默认为垂直方向
    @required this.direction, 
    List<Widget> children = const <Widget>[],
  })
    
  const Expanded({
    // 组件的弹性系数，为0或者null表示这个组件没有弹性，大于0时按各个组件的弹性系数比例来分配空间
     int flex = 1,  
     @required Widget child,
  })
  ```

#### 流式布局（Wrap、Flow）

- Wrap

  使用Row、Column、Flex布局的时候，如果子组件超出父组件的范围，会报错，不会自动换行。Wrap和它们几乎相同，但是可以自动换行，还多了几个特有属性

  ```dart
  Wrap({
    ...
    this.direction = Axis.horizontal,
    this.alignment = WrapAlignment.start,
    this.spacing = 0.0, // 主轴方向上Widget间距
    this.runAlignment = WrapAlignment.start, // 次轴方向对齐方式
    this.runSpacing = 0.0, // 次轴方向上Widget间距
    this.crossAxisAlignment = WrapCrossAlignment.start,
    this.textDirection,
    this.verticalDirection = VerticalDirection.down,
    List<Widget> children = const <Widget>[],
  })
  ```

- Flow可以自定义流式布局，布局性能比较高，但是相对复杂，一般使用Wrap代替

#### 层叠布局（Stack、Positioned）

Flutter中使用Stack和Positioned来实现绝对定位，和iOS中的frame布局类似。Stack允许子组件重叠，Position确定子组件相对父组件四个边的距离。

```dart
Stack({
  // 缺失必要定位的子组件的对齐方式
  this.alignment = AlignmentDirectional.topStart, 
  // 子组件的排列方向
  this.textDirection,
  // 缺失定位的子组件如何适应Stack的空间大小，loose为使用子组件本身大小，expand为扩展为Stack的大小
  this.fit = StackFit.loose,
  // 显示超出Stack显示空间的子组件，clip裁剪，visible显示
  this.overflow = Overflow.clip,
  List<Widget> children = const <Widget>[],
})
  
 const Positioned({
  Key key,
  this.left, 
  this.top,
  this.right,
  this.bottom,
  this.width,
  this.height,
  @required Widget child,
})
```

#### 对齐和相对定位（Align，alignment，Center）

简单的调整**一个**子元素在父元素中的位置可以式用Align

```dart
Align({
  Key key,
  this.alignment = Alignment.center,
  this.widthFactor, // 子组件的尺寸缩放因子
  this.heightFactor,
  Widget child,
})
```



- alignment

  AlignmentGeometry是矩阵，alignment矩阵上的一个点，alignment继承至AlignmentGeometry。`Alignment(this.x, this.y)`

  左上顶点：`Alignment(-1, -1)`，

  右上顶点：`Alignment(1, -1)`，

  左下顶点：`Alignment(-1, 1)`，

  右下顶点：`Alignment(1, 1)`。

- FractionalOffset

  `FractionalOffset` 继承自 `Alignment`，它和 `Alignment`唯一的区别就是坐标原点不同！`FractionalOffset` 的坐标原点为矩形的左侧顶点，

- Center

  ```dart
  Center(
      widthFactor: 1,
      heightFactor: 1,
      child: Text("xxx"),
    ),
  ```

## 容器Widget

> 而容器类Widget一般只需要接收一个子Widget（child），他们直接或间接继承自（或包含）SingleChildRenderObjectWidget

#### 填充容器（padding、EdgeInsets）

```dart
Padding({
  ...
  EdgeInsetsGeometry padding,
  Widget child,
})
  
EdgeInsets四个常用方法
1. fromLTRB(double left, double top, double right, double bottom)：分别指定四个方向的填充。
2. all(double value) : 所有方向均使用相同数值的填充。
3. only({left, top, right ,bottom })：可以设置具体某个方向的填充(可以同时指定多个方向)。
4. symmetric({ vertical, horizontal })：用于设置对称方向的填充
```

#### 尺寸限制容器

- ConstrainedBox和BoxDecoration

  ConstrainedBox和BoxDecoration搭配使用，给子组件添加额外的约束

  ```dart
  ConstrainedBox(
    constraints: BoxConstraints(
      minWidth: double.infinity, //宽度尽可能大
      minHeight: 50.0 //最小高度为50像素
    ),
    child: Container(
        height: 5.0, 
        child: redBox 
    ),
  )
    
  BoxConstraints.tight(Size size)，可以生成给定大小的限制；
  const BoxConstraints.expand()可以生成一个尽可能大的用以填充另一个容器的BoxConstraints
  ```

  如果ConstrainedBox有嵌套，出现多重约束，则会把所有约束综合起来去一个同时满足的约束条件

  也可以通过UncontrainedBox来去除父级约束限制

- UncontrainedBox

  用来去除父级约束限制

  ```dart
  ConstrainedBox(
      constraints: BoxConstraints(minWidth: 60.0, minHeight: 100.0),  //父
      child: UnconstrainedBox( //“去除”父级限制
        child: ConstrainedBox(
          constraints: BoxConstraints(minWidth: 90.0, minHeight: 20.0),//子
          child: redBox,
        ),
      )
  )
  ```

- SizedBox

  用来给子组件设置固定的宽、高。ConstrainedBox也可以实现同样的效果，它们的实现原理都是通过创建一个RenderConstrainedBox部件实现的。

  ```dart
  SizedBox(
    width: 80.0,
    height: 80.0,
    child: redBox
  )
  ```

- 其他约束盒子

   - `AspectRatio`，它可以指定子组件的长宽比
   - `LimitedBox` 用于指定最大宽高
   - `FractionallySizedBox` 可以根据父容器宽高的百分比来设置子组件宽高

#### 装饰容器(DecoratedBox、Boxdecoration)

在绘制子组件前绘制一些装饰样式，Boxdecoration继承至抽象类Decoration，负责描述装饰样式。

```dart
const DecoratedBox({
  Decoration decoration,
  DecorationPosition position = DecorationPosition.background,
  Widget child
})
  
  BoxDecoration({
    Color color, //颜色
    DecorationImage image,//图片
    BoxBorder border, //边框
    BorderRadiusGeometry borderRadius, //圆角
    List<BoxShadow> boxShadow, //阴影,可以指定多个
    Gradient gradient, //渐变
    BlendMode backgroundBlendMode, //背景混合模式
    BoxShape shape = BoxShape.rectangle, //形状
  })
```



#### 变换（Transform）

Transform在绘制子组件之前，对子组件进行一些矩阵变换

```dart
Container(
  color: Colors.black,
  child: new Transform(
    alignment: Alignment.topRight, //相对于坐标系原点的对齐方式
    transform: new Matrix4.skewY(0.3), //沿Y轴倾斜0.3弧度
    child: new Container(
      padding: const EdgeInsets.all(8.0),
      color: Colors.deepOrange,
      child: const Text('Apartment for rent!'),
    ),
  ),
);
```

Transform提供一些命名构造方法，快速创建一些常见的变换对象

- 平移

  ```dart
  Transform.translate(
      offset: Offset(-20.0, -5.0),
      child: Text("Hello world"),
    )
  ```

- 旋转

  ```dart
  Transform.rotate(
      //旋转90度
      angle:math.pi/2 ,
      child: Text("Hello world"),
    )
  ```

- 缩放

  ```dart
  Transform.scale(
        scale: 1.5, //放大到1.5倍
        child: Text("Hello world")
    )
  ```

*注意：Transform发生在布局之后的绘制阶段，所以Transform的子组件的实际位置和大小并没有变化，只是显示效果发生了变化。*

- RatatedBox

   `RotatedBox`和`Transform.rotate`功能相似，它们都可以对子组件进行旋转变换，但是有一点不同：`RotatedBox`的变换是在layout阶段，会影响在子组件的位置和大小

#### Container容器

`Container`是一个组合类容器，它本身不对应具体的`RenderObject`，它是`DecoratedBox`、`ConstrainedBox、Transform`、`Padding`、`Align`等组件组合的一个多功能容器，所以我们只需通过一个`Container`组件可以实现同时需要装饰、变换、限制的场景。

```dart
Container({
  this.alignment,
  this.padding, //容器内补白，属于decoration的装饰范围
  Color color, // 背景色
  Decoration decoration, // 背景装饰
  Decoration foregroundDecoration, //前景装饰
  double width,//容器的宽度
  double height, //容器的高度
  BoxConstraints constraints, //容器大小的限制条件
  this.margin,//容器外补白，不属于decoration的装饰范围
  this.transform, //变换
  this.child,
})
```

注意：

 	1.  Container可以设置宽高，如果同时设置了宽高和Constraints，则以设置的宽高为准
 	2.  color和装饰decoration不能同时设置，否则会报错。因为制定color后，Container内部会自定创建一个decoration。

#### 框架容器

- Scaffold
- AppBar
- Tabbar和TabbarView
- Drawer
- FloatingActionButton
- BottomNavigaitonBar

#### 裁剪（Clip）

## 滚动Widget

## 功能型WIdget

## 事件处理和通知

## 动画

## 文件操作和网络请求